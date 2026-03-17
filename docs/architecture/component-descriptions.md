# Component Catalogue

This document describes the core components of the NovaMesh architecture. Components are grouped by their primary role and include key responsibilities, dependencies, and known gaps.

---

## Notation

- **Status**: LIVE / MIGRATING / IN-BUILD
- **Upstream**: Systems or components this component depends on
- **Downstream**: Components that depend on this component

---

## C1 — NovaDoor Firmware

| Field | Value |
|---|---|
| **Status** | LIVE |
| **Technology** | C / Embedded Linux / TensorFlow Lite |
| **Upstream** | AWS IoT Core (MQTT), OTA updates from Device Management |
| **Downstream** | Device Management Service (visitor event telemetry, lock state) |

**Responsibilities**:
- Person and motion detection — distinguishes people from background movement; triggers camera capture
- **On-device face recognition** (Insights / Premium tier, "privacy mode"): runs MobileFaceNet locally; no face data sent to cloud
- Electronic lock control — receives lock/unlock commands from cloud, or executes locally when in edge mode
- MQTT communication with the cloud over TLS

**Known gaps**:
- ML models can only be updated via a full firmware OTA — a security patch to the face recognition model requires a complete firmware release cycle (2–3 weeks minimum)
- OTA update failures affect ~3–5% of attempts; rollback is manual and has door lock safety implications
- Fail-secure vs. fail-safe behaviour during cloud outage is not yet user-configurable

---

## C2 — Legacy Monolith

| Field | Value |
|---|---|
| **Status** | MIGRATING |
| **Technology** | Python / Django 4.2 / PostgreSQL 15 (2.1TB) |
| **Upstream** | Stripe (legacy), Auth0 (legacy), SendGrid |
| **Downstream** | Web app (legacy endpoints), Admin Portal |

**Responsibilities** (remaining, pre-migration):
- Enterprise account and site management (B2B portal, enterprise billing, door fleet assignment)
- Admin tooling for customer support
- **Notification preferences** — still stored here; not yet migrated to the Notification Service
- Legacy subscription endpoints (dual-write target during migration)

**Migration status**:
- User auth: migrated to Identity Service (~60%)
- Consumer subscriptions: being migrated (~30%)
- Enterprise accounts and admin tooling: not started

**This is the highest-priority technical debt in the company.**

---

## C3 — Identity Service

| Field | Value |
|---|---|
| **Status** | LIVE |
| **Technology** | Go 1.22 / PostgreSQL 15 (`novamesh-users-db`) |
| **SLO** | 99.9% availability; p99 < 200ms |
| **Upstream** | Auth0 (IdP) |
| **Downstream** | API Gateway, Subscription logic, Device Management, Notification Service |

**Responsibilities**:
- User registration, login, MFA — wraps Auth0 with NovaMesh business logic
- Household member management (who lives at an address and owns a NovaDoor)
- Face profile metadata — stores references to enrolled face embeddings (actual embeddings live in the Data Store, C8)
- RBAC roles: consumer (`free`, `protect`, `insights`, `premium`) and enterprise roles

**Known gaps**:
- No explicit consent tracking for biometric face data enrollment (required for GDPR/BIPA compliance)
- Profile data migration from the monolith is ~60% complete

---

## C4 — Device Management Service

| Field | Value |
|---|---|
| **Status** | LIVE |
| **Technology** | Go 1.22 / DynamoDB / AWS IoT Core |
| **SLO** | 99.9% availability; lock command delivery < 2s p95 |
| **Upstream** | AWS IoT Core, AWS IoT Device Shadow, S3 (firmware artefacts) |
| **Downstream** | Facial Recognition Service (visitor event routing), Notification Service |

**Responsibilities**:
- NovaDoor device registration and provisioning
- Device state management (online/offline, firmware version, lock state)
- OTA firmware update scheduling and delivery
- **Door lock/unlock command dispatch** — receives commands from Access Rules Engine and mobile app; delivers via MQTT to device

**Known gaps**:
- No fallback command path if AWS IoT Core is unavailable; lock commands are dropped
- DynamoDB hot-partition issues during mass OTA campaigns (> 10K simultaneous updates)
- No automated OTA rollback on failure threshold breach

---

## C5 — Facial Recognition Service

| Field | Value |
|---|---|
| **Status** | IN-BUILD (beta, ~8,000 opted-in devices) |
| **Technology** | Python 3.12 / MobileFaceNet / AWS Rekognition (external) |
| **Upstream** | Kafka (visitor event stream with video frames), Data Store (enrolled face embeddings), AWS Rekognition |
| **Downstream** | Access Rules Engine (recognition result), Notification Service (face label for alert) |

**Responsibilities**:
- Receive visitor face frame events from NovaDoor devices (via Kafka)
- Match visitor face against enrolled household embeddings
- Return recognition result: matched identity (name, confidence), unknown visitor, or no face detected
- Two operating modes:
  - **Cloud mode** (default): frames processed via AWS Rekognition; no abstraction layer
  - **Internal model** (beta): MobileFaceNet matching against local embeddings in `novamesh-faces-db`
- Biometric data audit logging

**Known gaps**:
- AWS Rekognition is a direct dependency with no abstraction layer — API changes, outages, or pricing changes hit immediately
- False accept rate ~2% (target: < 0.1%); false reject rate ~6% (target: < 3%)
- No biometric data deletion pipeline; cannot guarantee complete deletion of embeddings on user request
- Consent verification is not enforced at the API level

---

## C6 — Access Rules Engine

| Field | Value |
|---|---|
| **Status** | IN-BUILD |
| **Technology** | Python 3.12 / PostgreSQL |
| **Upstream** | Facial Recognition Service (recognition results), Device Management (lock commands), Identity Service (entitlement checks) |
| **Downstream** | Device Management Service (lock/unlock commands), Notification Service (rule-triggered alerts) |

**Responsibilities**:
- Real-time rule evaluation triggered by face recognition results
- Rule types: auto-unlock, auto-alert, time-based access, block list
- Rule configuration stored per-household / per-door in PostgreSQL

**Known gaps**:
- Basic auto-unlock rules are in production (beta); complex conditional rules are in development
- No safety override: a misconfigured rule could unlock the door unexpectedly or lock residents out
- Rule execution has no idempotency; a repeated recognition event could trigger duplicate unlock commands

---

## C7 — Notification Service

| Field | Value |
|---|---|
| **Status** | LIVE |
| **Technology** | Node.js 20 / Redis / Firebase FCM / SendGrid |
| **SLO** | Push: < 10s delivery p95 |
| **Upstream** | AWS SNS (event source), Firebase FCM, SendGrid, **Legacy Monolith DB** (notification preferences) |
| **Downstream** | End users (push / email / SMS) |

**Responsibilities**:
- **Visitor alert notifications**: real-time push with face recognition result (name or "Unknown visitor"), video thumbnail, and one-tap remote unlock
- Multi-channel notification delivery (push, email, SMS)
- Notification preference enforcement (opt-in/out per channel per event type)

**Known gaps**:
- Notification preferences are still read from the monolith database — a hidden coupling that means a monolith failure disrupts visitor alerts
- No dead-letter queue for failed notifications; delivery failures are logged but not retried

---

## C8 — Data Store

| Field | Value |
|---|---|
| **Status** | IN-BUILD |
| **Technology** | PostgreSQL + pgvector (`novamesh-faces-db`), S3 (video), Apache Kafka / MSK (event stream) |
| **SLO** | Kafka: 99.9% availability |
| **Upstream** | All services (event producers) |
| **Downstream** | Facial Recognition Service (training / lookup), analytics |

**Responsibilities**:
- **Face embedding store** (`novamesh-faces-db`): enrolled face embeddings per household, subject to biometric data regulations (GDPR, BIPA)
- **Video storage**: encrypted S3 buckets per household with tier-appropriate retention (7 / 30 / 90 / 365 days)
- **Visitor event stream**: real-time Kafka pipeline — visitor face frames, motion events, door state changes, recognition results
- Recognition audit log

**Known gaps**:
- No biometric data governance: face embeddings lack classification, lineage, or automated deletion; GDPR/BIPA deletion workflows are manual
- Video deletion on subscription downgrade is not automated
- No data residency controls for EU customers
