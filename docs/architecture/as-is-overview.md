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
The components responsible for machine learning, inference, and AI-driven features — primarily facial recognition, visitor intelligence, and access rule execution.

### Zone 4: Foundation
Shared infrastructure: data stores, messaging, observability, and third-party integrations.

---

## Zone 1: Customer-Facing Layer

### Web Application `[LIVE]`
- React SPA served via CDN (Cloudflare)
- Connects to the API Gateway
- Handles consumer and enterprise dashboards
- Features: face enrollment wizard, visitor history, live camera view, access rule configuration, video clip playback, enterprise multi-door management
- **Known issue**: Several pages still call the legacy Django monolith API directly, bypassing the API Gateway

### Mobile Apps — iOS & Android `[LIVE]`
- React Native codebase (shared ~70% of logic)
- Push notifications via Firebase Cloud Messaging (real-time "someone at your door" alerts with face label)
- NovaDoor provisioning flow uses a local Bluetooth handshake for initial Wi-Fi setup
- Live camera view with two-way audio
- Remote door unlock/lock controls
- **Known issue**: Android deep-link handling has a race condition with the auth flow during cold-start

### NovaDoor Firmware `[LIVE]`
- Custom Linux-based OS on the NovaDoor hardware
- Communicates with the cloud via MQTT over TLS
- Runs local inference models (TensorFlow Lite): person detection, on-device face recognition (Protect tier and above)
- Electronic lock control: receive lock/unlock commands from cloud or local edge decision
- OTA updates via the Device Management Service
- **Known issue**: OTA update failures affect ~3–5% of attempts; rollback is manual — with safety implications for door lock state

### Third-Party Integrations — Consumer `[LIVE]`
- Amazon Alexa skill ("Alexa, who's at the door?")
- Google Home integration
- Apple HomeKit (via HomeKit Accessory Protocol bridge on NovaDoor)
- IFTTT webhook endpoint (trigger automations based on visitor events)

---

## Zone 2: Core Platform Services

### Legacy Django Monolith `[MIGRATING]`
The original application. Still runs in production handling:
- Portions of user authentication (being migrated to Identity Service)
- Legacy subscription logic (being migrated to Subscription Service)
- Consumer door dashboard data (some endpoints still active)
- Admin tooling for customer support
- B2B (Enterprise) account and site management (not yet migrated)

The monolith runs on AWS ECS (single container), backed by a PostgreSQL RDS instance (`novamesh-monolith-db`). It has no horizontal scaling; vertical scaling has been the workaround.

**This is the highest-priority technical debt in the company.**

### Identity & User Management Service `[LIVE]`
- Newly extracted microservice (Go)
- Auth0 as the identity provider (external); this service wraps it with NovaMesh business logic
- Handles: registration, login, MFA, session tokens, user profiles, RBAC roles
- Manages household member profiles and face enrollment metadata (which faces belong to which household/organisation)
- Owns the `users` and `households` domain
- Postgres database: `novamesh-users-db`
- **Gap**: Enterprise multi-tenancy in RBAC is partially implemented; tenant isolation relies on application-level filtering rather than database-level separation

### Subscription & Billing Service `[MIGRATING]`
- Partially extracted from the monolith (Node.js / Express)
- Stripe as the payment processor (external)
- Handles: subscription creation, plan changes, renewals, invoicing, refunds
- Subscription tier determines which AI features are available (number of enrolled faces, history length, privacy mode, etc.)
- Currently has **dual-write** to both its own PostgreSQL DB and the monolith DB during migration
- **Known issue**: Dual-write occasionally produces inconsistent subscription states, causing users to be granted or denied features incorrectly

### Device Management Service `[LIVE]`
- Go microservice
- Manages NovaDoor device registry, firmware versions, OTA update scheduling
- Handles door lock/unlock command dispatch to devices via AWS IoT Core
- MQTT broker: AWS IoT Core
- Device shadow / state sync: AWS IoT Device Shadow (tracks: online/offline, lock state, firmware version, camera status)
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
- Core use case: real-time "visitor at door" alerts with face recognition result (name or "Unknown visitor"), video thumbnail, and one-tap remote unlock
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
- NovaMesh AI personalisation layer (PLANNED) will feed visitor engagement signals into Customer.io

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

**Facial Recognition Engine** `[IN-BUILD]`
- Identifies visitors by comparing captured face frames against enrolled face embeddings
- Model: MobileFaceNet-based embedding model + cosine similarity matching
- Two operating modes:
  - **Cloud mode** (default): frames sent to cloud for recognition; lower latency than edge-only for complex cases
  - **Edge mode** (Insights/Premium tier): recognition runs entirely on-device; no face data leaves the device
- Cloud recognition via AWS Rekognition (external) as temporary measure while internal model matures
- Internal embedding store: PostgreSQL with `pgvector` extension (`novamesh-faces-db`)
- **Status**: In beta with ~8,000 opted-in devices; false accept rate ~2%, false reject rate ~6% (targets: FAR < 0.1%, FRR < 3%)

**Visitor Intelligence Engine** `[IN-BUILD]`
- Analyses visitor patterns to provide insights: expected delivery windows, frequent visitors, unusual access times
- Package detection: classifies whether a visitor is carrying a parcel
- Model: YOLOv8-based object detection (package, person, vehicle)
- Training data: labelled video frames from opted-in devices
- **Status**: Package detection in beta (~2,000 opted-in devices); visitor pattern analysis is proof of concept

**Access Rules Engine** `[IN-BUILD]`
- Executes automated actions based on facial recognition results
- Rules examples: "auto-unlock when it's Alice", "alert + record when an unknown visitor arrives between 10pm–6am", "send welcome message when delivery driver arrives", "deny access to blocked faces and call emergency contact"
- Rule evaluation: real-time, triggered by face recognition result events
- Rules stored per-household / per-door in PostgreSQL
- **Status**: Basic auto-unlock rules in production (beta); complex conditional rules in development

**Support Chatbot** `[IN-BUILD]`
- LLM-powered customer support assistant
- RAG (Retrieval Augmented Generation) over Zendesk knowledge base
- Embedding model: OpenAI text-embedding-3-large
- Vector store: Pinecone

**AI Orchestration Service** `[IN-BUILD]`
- Central service for managing AI feature requests, routing to models, tracking usage
- Handles rate limiting across AI vendor APIs (Rekognition, OpenAI for support chatbot)
- Provides a single internal API for all AI capabilities
- **Status**: ~40% complete; currently a thin routing layer only

### Edge AI Runtime `[LIVE — PARTIAL]`
- TensorFlow Lite models deployed to NovaDoor devices
- Current live models: person detection (motion classification), on-device face recognition (MobileFaceNet, for Insights/Premium tier privacy mode)
- Model update mechanism: bundled with firmware OTA (not independently updatable)
- **Gap**: No ability to update ML models independently of firmware; high friction for model iteration, and critical for biometric security patches

---

## Zone 4: Foundation

### Data Platform `[IN-BUILD]`
- **Data Lake**: S3 + AWS Glue + Athena — stores video clips, visitor event logs, face recognition audit logs
- **Stream Processing**: Apache Kafka (MSK) for real-time visitor event streams
- **Data Warehouse**: Snowflake (being set up; not yet fully operational)
- **ETL**: Airbyte for pulling data from SaaS tools (HubSpot, Zendesk, Shopify) into the warehouse
- **Face Embedding Store**: PostgreSQL with `pgvector` (`novamesh-faces-db`) — stores enrolled face embeddings per household; biometric data subject to strict access controls
- **Video Storage**: S3 with per-household prefix and encryption at rest; lifecycle policies for tier-appropriate retention (7 days / 30 days / 90 days / 1 year)
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
| Biometric data (face embeddings) lacks unified governance and deletion pipeline | Critical | Zone 3, Zone 4 |
| OTA failure on door locks has physical safety implications | High | Zone 1 |
| No independent ML model update path (edge) — critical for biometric security patches | High | Zone 3 |
| AWS Rekognition dependency — no abstraction layer for face recognition | High | Zone 3 |
| Dual-write inconsistency in Subscription Service causes incorrect feature gating | High | Zone 2 |
| Enterprise tenant isolation gap | High | Zone 2 |
| Door lock/unlock command path has no local fallback if cloud unreachable | High | Zone 1, Zone 2 |
| Data governance inconsistency for biometric data across services | High | Zone 4 |
| Missing SLOs / unclear on-call ownership for AI components | Medium | Zone 3, Zone 4 |
