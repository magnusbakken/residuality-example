# Component Catalogue

This document provides a detailed reference for every major component in the NovaMesh architecture. Components are grouped by domain and include ownership, dependencies, and current health status.

---

## Notation

- **Owner**: Engineering team responsible for this component
- **Status**: LIVE / MIGRATING / IN-BUILD / PLANNED
- **SLO**: Service Level Objective (if defined)
- **Upstream**: Components or external systems this component depends on
- **Downstream**: Components that depend on this component

---

## C1 — Identity & User Management Service

| Field | Value |
|---|---|
| **Owner** | Platform Engineering |
| **Status** | LIVE |
| **Language / Runtime** | Go 1.22 |
| **Database** | PostgreSQL 15 (`novamesh-users-db`) |
| **SLO** | 99.9% availability; p99 latency < 200ms |
| **Upstream** | Auth0 (IdP), AWS Secrets Manager |
| **Downstream** | API Gateway, Subscription Service, Device Management, Notification Service, Legacy Monolith |

**Responsibilities**:
- User registration, login, password reset, MFA
- OAuth2 / OIDC token issuance (via Auth0)
- User profile management (name, preferences, communication settings)
- RBAC: consumer roles (`free`, `protect`, `insights`, `premium`) and enterprise roles (`org_admin`, `site_manager`, `viewer`)
- Session token management

**Known gaps**:
- Enterprise tenant isolation relies on `org_id` application-level filtering; no row-level security in DB
- Profile data migration from the monolith is ~60% complete; some users still have split data

---

## C2 — Legacy Django Monolith

| Field | Value |
|---|---|
| **Owner** | Platform Engineering (shared / diminishing) |
| **Status** | MIGRATING |
| **Language / Runtime** | Python 3.11 / Django 4.2 |
| **Database** | PostgreSQL 15 (`novamesh-monolith-db`, 2.1TB) |
| **SLO** | None formally defined |
| **Upstream** | Stripe, Auth0 (legacy), SendGrid, AWS S3 |
| **Downstream** | Web App (legacy endpoints), Admin Portal, Metabase |

**Responsibilities** (remaining, pre-migration):
- Enterprise account management (B2B portal, enterprise billing)
- Legacy subscription endpoints (dual-write target during migration)
- Admin tooling for customer support
- Legacy webhook handlers

**Migration status**:
- User auth: migrated to C1 (~60%)
- Consumer subscriptions: being migrated to C6 (~30%)
- Enterprise accounts: not started
- Admin tooling: not started, blocked on enterprise migration

---

## C3 — Device Management Service

| Field | Value |
|---|---|
| **Owner** | Platform Engineering |
| **Status** | LIVE |
| **Language / Runtime** | Go 1.22 |
| **Database** | DynamoDB (device registry, state); S3 (firmware artefacts) |
| **SLO** | 99.9% availability; OTA scheduling < 5 min delay |
| **Upstream** | AWS IoT Core, AWS IoT Device Shadow, S3 |
| **Downstream** | AI Platform, Data Platform (telemetry), Notification Service |

**Responsibilities**:
- Device registration and provisioning
- Device shadow / state management (online, offline, firmware version, configuration)
- OTA firmware update scheduling, delivery, and status tracking
- Fleet management (mass update campaigns)
- MQTT message routing from Hub devices

**Known gaps**:
- DynamoDB hot-partition issue during mass OTA campaigns (> 10K simultaneous updates)
- OTA rollback is manual; no automated rollback on failure threshold breach
- No independent model update path for edge AI models

---

## C4 — API Gateway

| Field | Value |
|---|---|
| **Owner** | Platform Engineering |
| **Status** | LIVE |
| **Technology** | AWS API Gateway + Lambda Authoriser |
| **SLO** | 99.95% availability; p99 latency < 100ms (gateway overhead) |
| **Upstream** | Identity Service (JWT validation) |
| **Downstream** | All microservices |

**Responsibilities**:
- Unified ingress for all external API calls (web app, mobile apps, third-party integrations)
- JWT authentication and authorisation enforcement
- Rate limiting (per-user and per-IP)
- Request routing and load balancing to microservices

**Known gaps**:
- Several legacy web app pages call the monolith directly, bypassing this gateway
- Service-to-service calls within the cluster bypass the gateway (no internal service mesh yet)

---

## C5 — Notification Service

| Field | Value |
|---|---|
| **Owner** | Platform Engineering |
| **Status** | LIVE |
| **Language / Runtime** | Node.js 20 |
| **Database** | Redis (queue/deduplication); notification preferences: monolith DB (being migrated) |
| **SLO** | Push: < 30s delivery p95; Email: best-effort |
| **Upstream** | AWS SNS (event source), Firebase FCM, SendGrid, Twilio |
| **Downstream** | End users (push/email/SMS) |

**Responsibilities**:
- Multi-channel notification delivery (push, email, SMS, in-app)
- Notification preference enforcement
- Deduplication and rate limiting of notifications
- Delivery status tracking

**Known gaps**:
- Notification preferences are read from two sources (monolith DB and in-memory cache); inconsistencies occur
- No dead-letter queue for failed notifications; delivery failures are logged but not retried systematically

---

## C6 — Subscription & Billing Service

| Field | Value |
|---|---|
| **Owner** | Platform Engineering |
| **Status** | MIGRATING |
| **Language / Runtime** | Node.js 20 / Express |
| **Database** | PostgreSQL 15 (`novamesh-billing-db`); dual-write to `novamesh-monolith-db` |
| **SLO** | None formally defined (migration in progress) |
| **Upstream** | Stripe, Identity Service |
| **Downstream** | Notification Service, AI Platform (entitlement checks), Legacy Monolith (dual-write) |

**Responsibilities**:
- Subscription lifecycle management (create, upgrade, downgrade, cancel)
- Payment method management via Stripe
- Subscription entitlement signals for feature gating
- Invoice generation and billing history
- Webhook handling from Stripe (payment succeeded, failed, subscription events)

**Known gaps**:
- Dual-write produces inconsistent states when one write succeeds and the other fails (~0.3% of operations)
- No idempotency on Stripe webhook processing (duplicate event handling possible)
- Enterprise billing (custom contracts, volume discounts) still in the monolith

---

## C7 — E-Commerce (Shopify)

| Field | Value |
|---|---|
| **Owner** | Marketing / Operations (external platform) |
| **Status** | LIVE |
| **Technology** | Shopify Plus + ShipBob |
| **Upstream** | Shopify Payments, Stripe |
| **Downstream** | Subscription Service (post-purchase webhook), ShipBob (fulfilment) |

**Responsibilities**:
- Hardware product catalogue and storefront
- Order management and payment processing
- Fulfilment via ShipBob 3PL
- Post-purchase trigger: sends webhook to NovaMesh API → Subscription Service to activate subscription trial

**Known gaps**:
- Shopify is an externally managed service; platform outages are outside NovaMesh's control
- No fallback if the post-purchase webhook fails (subscription activation may not trigger)
- Limited data visibility into customer journey pre-purchase from NovaMesh systems

---

## C8 — AI Orchestration Service

| Field | Value |
|---|---|
| **Owner** | AI/ML Team |
| **Status** | IN-BUILD (~40% complete) |
| **Language / Runtime** | Python 3.12 / FastAPI |
| **Database** | Redis (caching, rate limit counters); PostgreSQL (AI request logs) |
| **SLO** | Target: 99.5% availability; p95 latency < 3s |
| **Upstream** | OpenAI API, Anthropic API, internal ML model endpoints |
| **Downstream** | AI Assistant, Support Chatbot, Anomaly Detection, Personalisation Engine |

**Responsibilities**:
- Single internal API surface for all AI capabilities
- Routing requests to appropriate models (OpenAI, Anthropic, internal)
- Rate limiting and cost tracking across AI vendor APIs
- Fallback logic between AI providers
- Response caching for common requests

**Known gaps**:
- Currently a thin routing layer; fallback logic and caching are not yet implemented
- Cost tracking is not yet connected to subscription entitlement checks
- No circuit breaker on external AI API calls

---

## C9 — Anomaly Detection Engine

| Field | Value |
|---|---|
| **Owner** | AI/ML Team |
| **Status** | IN-BUILD (beta, ~2,000 opted-in devices) |
| **Language / Runtime** | Python 3.12 |
| **Model** | Isolation Forest + LSTM ensemble |
| **Upstream** | Kafka (device telemetry stream), Data Lake (training data) |
| **Downstream** | Notification Service (alert dispatch), AI Orchestration Service |

**Responsibilities**:
- Real-time detection of anomalous network behaviour (intrusion, device compromise, unusual traffic)
- Hardware anomaly detection (thermal, power, connectivity patterns)
- Alert generation with severity scoring
- Model retraining pipeline (weekly)

**Known gaps**:
- Beta rollout only; most customers do not yet receive anomaly alerts
- Model retraining pipeline is not automated; manual trigger required
- False positive rate is ~8% (target: < 2%)

---

## C10 — Predictive Maintenance Engine

| Field | Value |
|---|---|
| **Owner** | AI/ML Team |
| **Status** | IN-BUILD (proof of concept) |
| **Language / Runtime** | Python 3.12 |
| **Model** | Gradient Boosted Trees (XGBoost) |
| **Upstream** | Data Lake (historical telemetry), Device Management Service |
| **Downstream** | Notification Service, AI Orchestration Service |

**Responsibilities**:
- Predict hardware failures 7–30 days in advance
- Trigger proactive replacement or support workflows
- Feed into customer success workflows for enterprise accounts

**Known gaps**:
- No production inference yet; only offline batch analysis
- Insufficient labelled failure data to achieve target accuracy (< 10% false negatives)

---

## C11 — AI Assistant

| Field | Value |
|---|---|
| **Owner** | AI/ML Team + Mobile/Frontend |
| **Status** | IN-BUILD |
| **Language / Runtime** | Python 3.12 (backend); React Native (frontend widget) |
| **Upstream** | OpenAI GPT-4o API (primary), Anthropic Claude API (fallback), Device Management Service, Subscription Service |
| **Downstream** | Device Management Service (command execution), Notification Service |

**Responsibilities**:
- Natural language home control ("Turn off lights at bedtime")
- Subscription and account queries ("What's my current plan?")
- Contextual home automation recommendations
- LLM function calling to execute device commands

**Known gaps**:
- Direct dependency on OpenAI API; no abstraction layer in place
- Function call safety controls (preventing destructive commands) are not fully tested
- No rate limiting per user on AI assistant calls (cost risk)

---

## C12 — Support Chatbot

| Field | Value |
|---|---|
| **Owner** | AI/ML Team |
| **Status** | IN-BUILD |
| **Language / Runtime** | Python 3.12 |
| **Upstream** | OpenAI embeddings API, Pinecone (vector store), Zendesk knowledge base |
| **Downstream** | Zendesk (ticket creation for escalation) |

**Responsibilities**:
- First-line customer support via chat
- RAG over NovaMesh knowledge base
- Escalation to human agent via Zendesk ticket creation
- User account and subscription query resolution

**Known gaps**:
- Knowledge base is a static snapshot; not continuously updated
- No live access to device state or real-time subscription data
- No feedback loop to improve RAG quality over time

---

## C13 — Data Platform

| Field | Value |
|---|---|
| **Owner** | Platform Engineering (with AI/ML Team) |
| **Status** | IN-BUILD |
| **Technology** | S3 (Data Lake), AWS Glue, Athena, Apache Kafka (MSK), Snowflake, Airbyte |
| **SLO** | Kafka: 99.9% availability; Data Lake: best-effort |
| **Upstream** | All microservices (event producers), Shopify (via Airbyte), HubSpot (via Airbyte), Zendesk (via Airbyte) |
| **Downstream** | AI/ML models (training data), Snowflake (BI/analytics), Metabase |

**Responsibilities**:
- Real-time event streaming (device telemetry, user events, business events)
- Data lake storage and cataloguing
- ETL/ELT from SaaS platforms
- Data warehouse for analytics and reporting

**Known gaps**:
- Snowflake not yet fully operational
- Data governance (classification, lineage, access control) not implemented
- GDPR/CCPA deletion workflows are manual and ad hoc

---

## C14 — Edge AI Runtime (Hub Firmware)

| Field | Value |
|---|---|
| **Owner** | Firmware Team + AI/ML Team |
| **Status** | LIVE (partial — limited model set) |
| **Technology** | TensorFlow Lite, custom Linux OS |
| **Upstream** | OTA update mechanism (bundled with firmware) |
| **Downstream** | Device Management Service (telemetry), AI Platform (aggregated signals) |

**Responsibilities**:
- Local inference for privacy-preserving AI processing
- Current models: anomaly detection (lightweight), voice wake-word detection
- Runs entirely on-device; no cloud round-trip required for supported features

**Known gaps**:
- ML models can only be updated via a full firmware OTA
- No A/B testing capability for edge models
- Limited compute; larger models cannot run locally

---

## C15 — Marketing & CRM Platform

| Field | Value |
|---|---|
| **Owner** | Marketing |
| **Status** | LIVE (external) |
| **Technology** | HubSpot CRM, Customer.io |
| **Upstream** | Shopify (purchase data via webhook), NovaMesh API (customer events), Airbyte (sync) |
| **Downstream** | Email/SMS campaigns (via Customer.io), Sales team (HubSpot) |

**Responsibilities**:
- Customer and lead database
- Lifecycle email automation (onboarding, upsell, win-back, renewal)
- Sales pipeline management
- Marketing campaign execution

**Known gaps**:
- AI personalisation layer not yet connected
- Customer event data from NovaMesh products is incomplete (many events not tracked)
- GDPR consent state is not reliably propagated from NovaMesh systems to HubSpot/Customer.io

---

## C16 — Observability & Operations

| Field | Value |
|---|---|
| **Owner** | DevOps/SRE |
| **Status** | LIVE |
| **Technology** | Datadog (APM, logs, tracing, dashboards), PagerDuty (alerting) |
| **Upstream** | All services (metrics, logs, traces) |
| **Downstream** | On-call engineers |

**Known gaps**:
- Legacy monolith has minimal distributed tracing
- SLOs are only formally defined for C1, C3, C4
- On-call ownership is undefined for C6, C8, C9, C10, C11, C12
