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
- Household member management — tracks which people live at an address and own a NovaDoor
- Face profile metadata — stores references to enrolled face embeddings (actual embeddings live in C13 / novamesh-faces-db)
- RBAC: consumer roles (`free`, `protect`, `insights`, `premium`) and enterprise roles (`org_admin`, `site_manager`, `viewer`)
- Session token management

**Known gaps**:
- Enterprise tenant isolation relies on `org_id` application-level filtering; no row-level security in DB
- Profile data migration from the monolith is ~60% complete; some users still have split data
- No explicit consent tracking for biometric face data enrollment (required for BIPA / GDPR compliance)

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
- Enterprise account and site management (B2B portal, enterprise billing, door fleet assignment)
- Legacy subscription endpoints (dual-write target during migration)
- Admin tooling for customer support
- Legacy webhook handlers
- Notification preferences (still stored here; not yet migrated to C5's own store)

**Migration status**:
- User auth: migrated to C1 (~60%)
- Consumer subscriptions: being migrated to C6 (~30%)
- Enterprise accounts and site management: not started
- Admin tooling: not started, blocked on enterprise migration

---

## C3 — Device Management Service

| Field | Value |
|---|---|
| **Owner** | Platform Engineering |
| **Status** | LIVE |
| **Language / Runtime** | Go 1.22 |
| **Database** | DynamoDB (device registry, state); S3 (firmware artefacts) |
| **SLO** | 99.9% availability; OTA scheduling < 5 min delay; lock command delivery < 2s p95 |
| **Upstream** | AWS IoT Core, AWS IoT Device Shadow, S3 |
| **Downstream** | AI Platform, Data Platform (visitor event telemetry), Notification Service |

**Responsibilities**:
- NovaDoor device registration and provisioning
- Device shadow / state management (online, offline, firmware version, lock state, camera status)
- OTA firmware update scheduling, delivery, and status tracking
- Fleet management (mass update campaigns)
- **Door lock/unlock command dispatch** — receives commands from Access Rules Engine and mobile app; delivers via MQTT to device
- MQTT message routing from NovaDoor devices (visitor event telemetry, motion events, lock state changes)

**Known gaps**:
- DynamoDB hot-partition issue during mass OTA campaigns (> 10K simultaneous updates)
- OTA rollback is manual; no automated rollback on failure threshold breach — high severity because a bad OTA can brick door lock functionality
- No independent model update path for edge AI models (face recognition model updates require full firmware OTA)
- No fallback command path if AWS IoT Core is unavailable; lock commands are dropped

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
| **SLO** | Push: < 10s delivery p95; Email: best-effort |
| **Upstream** | AWS SNS (event source), Firebase FCM, SendGrid, Twilio |
| **Downstream** | End users (push/email/SMS) |

**Responsibilities**:
- **Visitor alert notifications**: real-time push with face recognition result (name or "Unknown visitor"), video thumbnail, and one-tap remote unlock action
- Multi-channel notification delivery (push, email, SMS, in-app)
- Notification preference enforcement (opt-in/out per channel per event type)
- Deduplication and rate limiting of notifications (prevent alert flooding during repeated motion events)
- Delivery status tracking

**Known gaps**:
- Notification preferences are read from two sources (monolith DB and in-memory cache); inconsistencies occur
- No dead-letter queue for failed notifications; delivery failures are logged but not retried systematically
- No batching strategy for high-frequency motion events — potential for alert fatigue

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
- **Feature entitlement signals** — determines which AI features are available based on tier:
  - Free: basic motion alerts, 7-day video
  - Protect: face recognition (up to 10 faces), auto-unlock, 30-day video
  - Insights: unlimited faces, visitor intelligence, privacy mode, 90-day video
  - Premium: all features + 1-year video history
- Invoice generation and billing history
- Webhook handling from Stripe (payment succeeded, failed, subscription events)

**Known gaps**:
- Dual-write produces inconsistent states when one write succeeds and the other fails (~0.3% of operations), causing incorrect feature gating
- No idempotency on Stripe webhook processing (duplicate event handling possible)
- Enterprise billing (custom contracts, volume discounts, multi-door pricing) still in the monolith

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
- NovaDoor product catalogue and storefront
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
| **SLO** | Target: 99.5% availability; p95 latency < 2s |
| **Upstream** | AWS Rekognition API, OpenAI embeddings API, internal ML model endpoints |
| **Downstream** | Facial Recognition Engine, Visitor Intelligence Engine, Access Rules Engine, Support Chatbot |

**Responsibilities**:
- Single internal API surface for all AI capabilities
- Routing face recognition requests between cloud (AWS Rekognition), internal model, and edge (based on tier and availability)
- Rate limiting and cost tracking across AI vendor APIs
- Fallback logic between AI providers
- Response caching for face recognition results (within a recognition session)

**Known gaps**:
- Currently a thin routing layer; fallback logic and caching are not yet implemented
- Cost tracking is not yet connected to subscription entitlement checks
- No circuit breaker on external AI API calls (AWS Rekognition)
- No abstraction layer: if AWS Rekognition API changes, every downstream service must update

---

## C9 — Facial Recognition Engine

| Field | Value |
|---|---|
| **Owner** | AI/ML Team |
| **Status** | IN-BUILD (beta, ~8,000 opted-in devices) |
| **Language / Runtime** | Python 3.12 |
| **Model** | MobileFaceNet embedding model + cosine similarity matching |
| **Upstream** | Kafka (visitor event stream with video frames), `novamesh-faces-db` (enrolled embeddings), AWS Rekognition (cloud fallback) |
| **Downstream** | Notification Service (recognition result for alert), Access Rules Engine (recognition result for rule evaluation), AI Orchestration Service, Data Platform |

**Responsibilities**:
- Receive visitor face frame events from NovaDoor devices (via Kafka)
- Extract face embedding from frame and compare against enrolled household embeddings
- Return recognition result: matched identity (name, confidence), unknown visitor, or no-face-detected
- Manage enrolled face embeddings: create, update, delete (for face enrollment flow)
- Biometric data audit logging (all recognition events logged for compliance)
- Model retraining pipeline (improves with opted-in feedback)

**Known gaps**:
- Beta rollout only; most customers are using AWS Rekognition as cloud fallback, not the internal model
- False accept rate ~2% (target: < 0.1%); false reject rate ~6% (target: < 3%)
- Model retraining pipeline is not automated; manual trigger required
- No biometric data deletion pipeline — cannot guarantee complete deletion of face embeddings on user request
- Consent verification before storing face embeddings is not enforced at the API level

---

## C10 — Visitor Intelligence Engine

| Field | Value |
|---|---|
| **Owner** | AI/ML Team |
| **Status** | IN-BUILD (proof of concept) |
| **Language / Runtime** | Python 3.12 |
| **Model** | YOLOv8 (object detection: package, person, vehicle) + pattern analysis |
| **Upstream** | Data Lake (historical visitor events), Device Management Service, Facial Recognition Engine |
| **Downstream** | Notification Service, AI Orchestration Service, Access Rules Engine |

**Responsibilities**:
- **Package detection**: identify whether a visitor is carrying a parcel (for delivery notifications)
- **Visitor pattern analysis**: build expected visitor profiles (time of day, frequency) and alert on anomalies
- **Familiar visitor suggestions**: prompt users to enroll frequently seen unknown faces
- Feed visitor intelligence context into Access Rules Engine

**Known gaps**:
- Package detection is in beta (~2,000 opted-in devices); false positive rate is ~12% (target: < 5%)
- Visitor pattern analysis is proof of concept only; no production inference yet
- Insufficient labelled training data for vehicle detection and courier classification

---

## C11 — Access Rules Engine

| Field | Value |
|---|---|
| **Owner** | AI/ML Team + Platform Engineering |
| **Status** | IN-BUILD |
| **Language / Runtime** | Python 3.12 (backend); React (rule configuration UI) |
| **Upstream** | Facial Recognition Engine (recognition results), Visitor Intelligence Engine (context), Device Management Service (lock commands), Subscription Service (entitlement checks) |
| **Downstream** | Device Management Service (lock/unlock commands), Notification Service (rule-triggered alerts) |

**Responsibilities**:
- Real-time rule evaluation triggered by face recognition results
- Rule types:
  - **Auto-unlock**: unlock door when a specific enrolled face is detected
  - **Auto-alert**: send alert with video clip when an unknown visitor arrives
  - **Time-based rules**: different actions depending on time of day (e.g., auto-unlock only during working hours)
  - **Block list**: deny access and alert when a specific face is detected
  - **Package delivery rules**: trigger notification when package detected, even without face recognition
- Rule configuration stored per-household / per-door in PostgreSQL
- Enterprise rules: access schedules, visitor clearance levels, emergency lockdown

**Known gaps**:
- Basic auto-unlock rules are in production (beta); complex conditional rules (time-based, context-aware) are in development
- No safety override: a misconfigured rule could unlock the door unexpectedly or lock residents out
- Rule execution has no idempotency; a repeated face recognition event could trigger duplicate unlock commands
- Function safety controls (preventing rules from being configured in dangerous ways) are not fully tested

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
- RAG over NovaMesh knowledge base (installation guides, face enrollment troubleshooting, access rules configuration)
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
| **Technology** | S3 (Data Lake + Video Storage), AWS Glue, Athena, Apache Kafka (MSK), Snowflake, Airbyte, PostgreSQL + pgvector (`novamesh-faces-db`) |
| **SLO** | Kafka: 99.9% availability; Data Lake: best-effort |
| **Upstream** | All microservices (event producers), Shopify (via Airbyte), HubSpot (via Airbyte), Zendesk (via Airbyte) |
| **Downstream** | AI/ML models (training data), Snowflake (BI/analytics), Metabase |

**Responsibilities**:
- Real-time event streaming (visitor events, door state changes, face recognition results, user events, business events)
- **Video storage**: encrypted S3 buckets per household with tier-appropriate retention policies (7 / 30 / 90 / 365 days)
- **Face embedding store** (`novamesh-faces-db`): enrolled face embeddings per household, with strict access controls; subject to biometric data regulations
- **Recognition audit log**: immutable log of all face recognition events (who was recognised, when, at which door, confidence score)
- Data lake storage and cataloguing for AI model training
- ETL/ELT from SaaS platforms
- Data warehouse for analytics and reporting

**Known gaps**:
- Snowflake not yet fully operational
- **No biometric data governance**: face embeddings lack classification, lineage, or automated deletion; GDPR/BIPA deletion workflows are manual and ad hoc
- Video deletion on subscription downgrade is not automated
- No data residency controls (relevant for EU customers subject to GDPR)

---

## C14 — Edge AI Runtime (NovaDoor Firmware)

| Field | Value |
|---|---|
| **Owner** | Firmware Team + AI/ML Team |
| **Status** | LIVE (partial — limited model set) |
| **Technology** | TensorFlow Lite, MobileFaceNet (quantised), custom Linux OS |
| **Upstream** | OTA update mechanism (bundled with firmware) |
| **Downstream** | Device Management Service (visitor event telemetry), AI Platform (recognition results when edge mode active) |

**Responsibilities**:
- **Person detection** (all tiers): distinguishes people from background motion; triggers camera capture and cloud notification
- **On-device face recognition** (Insights / Premium tier, privacy mode): runs MobileFaceNet locally; no face data sent to cloud
- Local door lock/unlock decision when cloud command is unavailable (configurable fail-secure / fail-safe mode)
- Runs entirely on-device; no cloud round-trip required for supported features in privacy mode

**Known gaps**:
- ML models can only be updated via a full firmware OTA — critical gap for biometric security patches (e.g., model poisoning vulnerability)
- No A/B testing capability for edge models
- On-device face recognition accuracy is ~4% lower than cloud model (hardware compute constraints)
- Fail-secure vs. fail-safe door lock behaviour during cloud outage is not yet configurable by end users

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
- Lifecycle email automation (onboarding, face enrollment prompts, upsell to Insights, win-back, renewal)
- Sales pipeline management
- Marketing campaign execution

**Known gaps**:
- AI personalisation layer not yet connected
- Customer event data from NovaDoor (visitor events, feature usage) is incomplete
- GDPR/BIPA consent state is not reliably propagated from NovaMesh systems to HubSpot/Customer.io

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
- No alerting on biometric data access anomalies (e.g., bulk face embedding queries)
