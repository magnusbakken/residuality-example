# As-Is Architecture Overview

This document describes NovaMesh's current architecture — a hybrid state reflecting the company's evolution from a validated MVP through rapid scale to an ongoing microservices migration.

> **Note**: This architecture is intentionally imperfect. It captures real-world constraints — technical debt, mid-migration components, and emerging AI capabilities. The purpose of the Residuality Theory workshop is not to critique it as "wrong," but to explore how it responds to stressors and what residues emerge.

---

## Architecture Status Legend

| Symbol | Meaning |
|---|---|
| LIVE | In production, serving real traffic |
| MIGRATING | Being rebuilt or extracted from the monolith |
| IN-BUILD | Active development, not yet in production |

---

## The Eight Components

The NovaMesh architecture can be understood as eight components across three layers:

### Edge Layer (the physical device)

**C1 — NovaDoor Firmware** `[LIVE]`

The physical NovaDoor device. Runs a custom embedded Linux OS with TensorFlow Lite models for person detection and (for Insights/Premium tier) on-device face recognition. Controls the electronic deadbolt lock, communicates with the cloud via MQTT over TLS, and receives OTA firmware updates.

Key tension: ML models can only be updated via full firmware OTA, meaning a biometric security patch requires a complete firmware release cycle. OTA failure affects ~3–5% of attempts and has door lock safety implications.

---

### Core Services Layer

**C2 — Legacy Monolith** `[MIGRATING]`

The original Django application. Still runs in production handling enterprise account management, admin tooling, legacy subscription endpoints, and — critically — notification preferences for the Notification Service. No horizontal scaling; single point of failure.

**This is the highest-priority technical debt.** Migration is underway but enterprise accounts and admin tooling haven't been started yet.

**C3 — Identity Service** `[LIVE]`

Extracted Go microservice. Wraps Auth0 with NovaMesh business logic: user auth, MFA, household member management, RBAC, and face profile metadata (references to enrolled embeddings). No explicit consent tracking for biometric data enrollment.

**C4 — Device Management Service** `[LIVE]`

Go microservice. Manages the fleet of 115K+ NovaDoor devices: device registry, OTA update scheduling, and — most critically — lock/unlock command dispatch via AWS IoT Core. No fallback path if IoT Core is unavailable; lock commands are dropped.

**C7 — Notification Service** `[LIVE]`

Node.js microservice. Sends real-time visitor alerts: push (Firebase FCM), email (SendGrid), and SMS. The core consumer value ("know who's at your door in real-time") depends on this service.

Hidden coupling: notification preferences are still read from the monolith database — this means a monolith failure disrupts visitor alerts even though these appear to be independent services.

---

### AI Layer

**C5 — Facial Recognition Service** `[IN-BUILD]`

Python/FastAPI service. The core of the NovaMesh product differentiation. Processes visitor face frames from Kafka, matches against enrolled face embeddings, and publishes recognition results to the Access Rules Engine and Notification Service.

Currently in beta with ~8,000 opted-in devices. Default cloud mode uses AWS Rekognition directly, with no abstraction layer. An internal MobileFaceNet model is in development as an alternative.

Key risks: false accept rate is ~2% (target < 0.1%); AWS Rekognition has no abstraction layer; no automated deletion pipeline for enrolled face embeddings.

**C6 — Access Rules Engine** `[IN-BUILD]`

Python service. Evaluates auto-unlock and alert rules in real-time, triggered by recognition results from C5. Dispatches lock/unlock commands via C4. Rules are stored per-household in PostgreSQL.

The chain C5 → C6 → C4 → C1 is the critical path for the product's core safety behaviour (auto-unlock). All four components must be working for auto-unlock to function — but they are affected by different stressors and owned by different teams.

---

### Data Layer

**C8 — Data Store** `[IN-BUILD]`

The biometric and event data backbone:
- **Face embedding store** (`novamesh-faces-db`, PostgreSQL + pgvector): enrolled face embeddings per household
- **Video storage** (S3): encrypted per-household video clips with tier-appropriate retention
- **Visitor event stream** (Kafka/MSK): real-time pipeline of visitor face frames, motion events, and recognition results

No unified biometric data governance: face embeddings lack automated deletion workflows, classification, or data residency controls. GDPR/BIPA deletion is currently manual.

---

## Key Architectural Risks

| Risk | Severity |
|---|---|
| Monolith is a single point of failure; notification preferences coupled to it | Critical |
| Biometric data (face embeddings) lacks deletion pipeline or data governance | Critical |
| AWS Rekognition is a direct dependency with no abstraction layer | High |
| OTA failure on door locks has physical safety implications | High |
| Auto-unlock chain (C5 → C6 → C4 → C1) has no end-to-end SLO | High |
| Lock command delivery has no fallback if cloud is unavailable | High |
| ML model updates coupled to full firmware OTA cycle | High |

---

## External Dependencies

| System | Role | Risk |
|---|---|---|
| AWS Rekognition | Cloud face recognition (default mode) | Very High — core differentiator; no abstraction layer; biometric data leaves NovaMesh |
| Auth0 | Identity provider | High — all authentication depends on this |
| AWS IoT Core | Lock command delivery + MQTT broker | High — no fallback |
| Firebase FCM | Push notification delivery | Medium — delays affect core UX |
| Stripe | Subscription billing | High — revenue critical |
