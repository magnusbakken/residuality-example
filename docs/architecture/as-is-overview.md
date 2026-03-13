# As-Is Architecture Overview

This document describes NovaMesh's current architecture — a **hybrid state** that reflects the company's evolution from a validated MVP through rapid scale to an ongoing microservices migration. Some components are battle-tested production systems; others are partially migrated or actively under construction.

> **Note for workshop participants**: This architecture is intentionally imperfect. It captures real-world constraints — technical debt, mid-migration components, vendor dependencies, and emerging capabilities. The purpose of the Residuality Theory workshop is not to critique the architecture as "wrong," but to systematically explore how it responds to stressors, and to discover what residues emerge from that process.

---

## Architecture Status Legend

| Symbol | Meaning |
|---|---|
| LIVE | In production, serving real traffic |
| MIGRATING | Being extracted from the monolith or rebuilt |
| IN-BUILD | Active development, not yet in production |
| PLANNED | Roadmap item, design in progress |

---

## System Zones

The NovaMesh architecture is organised across four zones:

### Zone 1: Customer-Facing Layer
How users and devices interact with NovaMesh systems.

### Zone 2: Core Platform Services
The domain services that implement business logic.

### Zone 3: AI & Intelligence Layer
The components responsible for machine learning, inference, and AI-driven features.

### Zone 4: Foundation
Shared infrastructure: data stores, messaging, observability, and third-party integrations.

---

## Zone 1: Customer-Facing Layer

### Web Application `[LIVE]`
- React SPA served via CDN (Cloudflare)
- Connects to the API Gateway
- Handles consumer and enterprise dashboards
- **Known issue**: Several pages still call the legacy Django monolith API directly, bypassing the API Gateway

### Mobile Apps — iOS & Android `[LIVE]`
- React Native codebase (shared ~70% of logic)
- Push notifications via Firebase Cloud Messaging
- Device provisioning flow (Wi-Fi onboarding) uses a local Bluetooth handshake with the Hub
- **Known issue**: Android deep-link handling has a race condition with the auth flow during cold-start

### NovaMesh Hub Firmware `[LIVE]`
- Custom Linux-based OS on the Hub hardware
- Communicates with the cloud via MQTT over TLS
- Runs local inference models (TensorFlow Lite)
- OTA updates via the Device Management Service
- **Known issue**: OTA update failures affect ~3–5% of attempts; rollback is manual

### Third-Party Integrations — Consumer `[LIVE]`
- Amazon Alexa skill
- Google Home integration
- Apple HomeKit (via HomeKit Accessory Protocol bridge on Hub)
- IFTTT webhook endpoint

---

## Zone 2: Core Platform Services

### Legacy Django Monolith `[MIGRATING]`
The original application. Still runs in production handling:
- Portions of user authentication (being migrated to Identity Service)
- Legacy subscription logic (being migrated to Subscription Service)
- Consumer device dashboard data (some endpoints still active)
- Admin tooling for customer support
- B2B (Enterprise) account management (not yet migrated)

The monolith runs on AWS ECS (single container), backed by a PostgreSQL RDS instance (`novamesh-monolith-db`). It has no horizontal scaling; vertical scaling has been the workaround.

**This is the highest-priority technical debt in the company.**

### Identity & User Management Service `[LIVE]`
- Newly extracted microservice (Go)
- Auth0 as the identity provider (external); this service wraps it with NovaMesh business logic
- Handles: registration, login, MFA, session tokens, user profiles, RBAC roles
- Owns the `users` domain
- Postgres database: `novamesh-users-db`
- **Gap**: Enterprise multi-tenancy in RBAC is partially implemented; tenant isolation relies on application-level filtering rather than database-level separation

### Subscription & Billing Service `[MIGRATING]`
- Partially extracted from the monolith (Node.js / Express)
- Stripe as the payment processor (external)
- Handles: subscription creation, plan changes, renewals, invoicing, refunds
- Currently has **dual-write** to both its own PostgreSQL DB and the monolith DB during migration
- **Known issue**: Dual-write occasionally produces inconsistent subscription states

### Device Management Service `[LIVE]`
- Go microservice
- Manages Hub device registry, firmware versions, OTA update scheduling
- MQTT broker: AWS IoT Core
- Device shadow / state sync: AWS IoT Device Shadow
- Stores device metadata in DynamoDB
- **Scaling issue**: At 115K+ devices, DynamoDB hot-partition issues appear during mass OTA rollouts

### E-Commerce Service `[LIVE — EXTERNAL]`
- **Shopify** store for hardware sales (novamesh.com/shop)
- Shopify Payments + Stripe as payment gateway
- Order fulfilment via ShipBob (3PL)
- Webhook integration: Shopify → NovaMesh API Gateway → Subscription Service (activates trial on hardware purchase)
- **Risk**: This is an externally managed service with no NovaMesh-controlled fallback

### Notification Service `[LIVE]`
- Node.js microservice
- Channels: push (Firebase FCM), email (SendGrid), SMS (Twilio)
- Event-driven: subscribes to SNS topics from other services
- Preference management is stored in the monolith DB (not yet migrated)
- **Known issue**: Notification preference data is inconsistently read from two sources

### Customer Support Service `[LIVE — EXTERNAL + INTERNAL]`
- Zendesk as external ticketing system
- Internal NovaMesh "Support Assistant" (AI chatbot, IN-BUILD) integrated into Zendesk
- Knowledge base: Zendesk Guide
- **Gap**: Support chatbot is trained on a static document snapshot; does not yet have live access to device state or subscription data

### Marketing & CRM `[LIVE — EXTERNAL]`
- HubSpot CRM for all customer and lead data
- Customer.io for lifecycle email automation
- NovaMesh AI personalisation layer (PLANNED) will feed signals into Customer.io

### API Gateway `[LIVE]`
- AWS API Gateway + Lambda authoriser
- Routes requests to microservices
- Rate limiting and JWT validation at the gateway
- **Gap**: Several internal service-to-service calls bypass the gateway (direct HTTP calls between services)

---

## Zone 3: AI & Intelligence Layer

### AI Platform `[IN-BUILD]`
The core of NovaMesh's product differentiation. Being built as a set of ML services orchestrated by a central **AI Orchestration Service**.

Sub-components:

**Anomaly Detection Engine** `[IN-BUILD]`
- Detects unusual network behaviour (potential intrusions, device malfunctions)
- Model: ensemble of isolation forest + LSTM
- Training data: device telemetry from the Data Lake
- Inference: near-real-time via Kafka stream consumer
- **Status**: Model in beta with ~2,000 opted-in devices; not yet serving all customers

**Predictive Maintenance Engine** `[IN-BUILD]`
- Predicts hardware failures based on device telemetry patterns
- Model: gradient boosted trees + feature engineering pipeline
- **Status**: Proof of concept; no production inference yet

**Energy Optimisation Engine** `[PLANNED]`
- Recommends home energy usage patterns
- Will integrate with smart plug telemetry and energy provider APIs
- **Status**: Design phase only

**Personalisation & Recommendation Engine** `[PLANNED]`
- Drives personalised product recommendations and in-app content
- Planned to use collaborative filtering + LLM-generated explanations
- **Status**: Not started

**AI Assistant (Natural Language Interface)** `[IN-BUILD]`
- Conversational interface for home control ("Turn off all lights at 10pm on weekdays")
- Built on OpenAI GPT-4o API (function calling)
- Also uses Anthropic Claude API as a fallback
- **Critical dependency**: No abstraction layer between the AI Assistant and OpenAI API; a breaking API change would require significant rework

**Support Chatbot** `[IN-BUILD]`
- LLM-powered customer support assistant
- RAG (Retrieval Augmented Generation) over Zendesk knowledge base
- Embedding model: OpenAI text-embedding-3-large
- Vector store: Pinecone

**AI Orchestration Service** `[IN-BUILD]`
- Central service for managing AI feature requests, routing to models, tracking usage
- Handles rate limiting across AI vendor APIs
- Provides a single internal API for all AI capabilities
- **Status**: ~40% complete; currently a thin routing layer only

### Edge AI Runtime `[LIVE — PARTIAL]`
- TensorFlow Lite models deployed to Hub devices
- Current live models: local anomaly detection (lightweight), voice wake word detection
- Model update mechanism: bundled with firmware OTA (not independently updatable)
- **Gap**: No ability to update ML models independently of firmware; high friction for model iteration

---

## Zone 4: Foundation

### Data Platform `[IN-BUILD]`
- **Data Lake**: S3 + AWS Glue + Athena
- **Stream Processing**: Apache Kafka (MSK) for real-time telemetry
- **Data Warehouse**: Snowflake (being set up; not yet fully operational)
- **ETL**: Airbyte for pulling data from SaaS tools (HubSpot, Zendesk, Shopify) into the warehouse
- **Status**: Data lake is live; Snowflake is partially operational; analytics tooling is still Metabase on top of the monolith DB

### Observability Stack `[LIVE]`
- Metrics: Datadog (APM + infrastructure)
- Logging: Datadog Log Management
- Tracing: Datadog distributed tracing (adopted by new microservices; legacy monolith has minimal tracing)
- Alerting: PagerDuty
- **Gap**: On-call rotations are not well-defined for all services; several components have no SLOs

### Infrastructure & Deployment `[LIVE]`
- Cloud: AWS (primary), with some GCP usage for ML workloads
- Container orchestration: Amazon EKS (Kubernetes)
- Legacy monolith: AWS ECS (not yet on EKS)
- Infrastructure as Code: Terraform
- CI/CD: GitHub Actions + ArgoCD (GitOps for EKS deployments)
- Secrets management: AWS Secrets Manager

### Internal Tooling `[LIVE]`
- Internal admin portal: built in the legacy monolith
- BI/Analytics: Metabase (on monolith DB, read replica)
- Documentation: Notion
- Issue tracking: Linear

---

## Key Architectural Risks (Current State)

| Risk | Severity | Affected Zone |
|---|---|---|
| Monolith is a single point of failure | Critical | Zone 2 |
| AI Platform vendor lock-in (OpenAI/Anthropic) | High | Zone 3 |
| Dual-write inconsistency in Subscription Service | High | Zone 2 |
| No independent ML model update path (edge) | High | Zone 3 |
| Enterprise tenant isolation gap | High | Zone 2 |
| OTA update failure rate | Medium | Zone 1 |
| Data governance inconsistency across services | Medium | Zone 4 |
| Direct service-to-service calls bypassing gateway | Medium | Zone 2 |
| Missing SLOs / unclear on-call ownership | Medium | Zone 4 |
| Shopify as uncontrolled e-commerce channel | Medium | Zone 2 |
