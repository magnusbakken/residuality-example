# Technology Stack

A reference catalogue of all technologies used across the NovaMesh platform.

---

## Cloud & Infrastructure

| Technology | Purpose | Notes |
|---|---|---|
| AWS (primary) | Cloud infrastructure | EKS, ECS, RDS, DynamoDB, S3, MSK, IoT Core, API Gateway, Lambda, Secrets Manager, SQS, SNS, Rekognition, Kinesis Video Streams |
| GCP (secondary) | ML workloads (Vertex AI, BigQuery experiments) | Limited use; being evaluated |
| Cloudflare | CDN, DDoS protection, DNS | Web app and API edge caching |
| Terraform | Infrastructure as Code | All AWS resources defined in Terraform |
| Amazon EKS | Kubernetes for microservices | New services deploy here |
| Amazon ECS | Legacy monolith container | Pre-EKS; migration planned |
| GitHub Actions | CI pipelines | Build, test, lint, Docker image publish |
| ArgoCD | GitOps CD for EKS | Declarative deployment from Git |

---

## Languages & Runtimes

| Language | Used For |
|---|---|
| Go 1.22 | Identity Service (C3), Device Management Service (C4) |
| Python 3.12 | AI services (Facial Recognition C5, Access Rules Engine C6) |
| Python 3.11 / Django 4.2 | Legacy Monolith (C2) |
| Node.js 20 | Notification Service (C7) |
| TypeScript / React | Web Application (including access rules configuration UI) |
| React Native | iOS and Android mobile apps |
| C / Embedded Linux | NovaDoor firmware |

---

## Databases & Storage

| Technology | Used By | Notes |
|---|---|---|
| PostgreSQL 15 | Identity Service (C3), Monolith (C2), Access Rules Engine (C6) | RDS managed instances |
| PostgreSQL 15 + pgvector | Data Store (C8 — `novamesh-faces-db`) | Stores enrolled face embeddings; biometric data — strict access controls required |
| DynamoDB | Device Management Service (C4) | NoSQL; hot-partition risk during mass OTA |
| Redis | Notification Service (C7 — queue and deduplication) | ElastiCache |
| S3 | Data Lake, firmware artefacts, ML model artefacts, **video clip storage** (per-household, encrypted, tiered retention) | |
| Snowflake | Data Warehouse | Being set up |

---

## Messaging & Streaming

| Technology | Used For | Notes |
|---|---|---|
| Apache Kafka (AWS MSK) | Visitor event stream (face frames, door events), business event bus | Core of real-time data platform; visitor events are the primary product data stream |
| AWS SNS | Notification triggers between services | Fan-out from service events |
| AWS SQS | Async job queues | Used by several services for background work |
| AWS IoT Core | MQTT broker for NovaDoor device communication | Device → Cloud messaging; also Cloud → Device for lock commands |
| AWS IoT Device Shadow | Device state synchronisation | Persistent device state: lock state, camera status, firmware version, online/offline |
| AWS Kinesis Video Streams | Real-time video streaming from NovaDoor to cloud | Used for live view feature; video is also written to S3 for clip storage |

---

## AI & Machine Learning

| Technology | Used For | Notes |
|---|---|---|
| AWS Rekognition | Facial recognition (cloud mode, current default) | External dependency; no internal abstraction layer — critical risk |
| MobileFaceNet (TFLite) | On-device face recognition (privacy mode, Insights/Premium tier) | Quantised model; runs on NovaDoor embedded NPU |
| MobileFaceNet (PyTorch) | Cloud facial recognition (internal model, in development) | Internal model being developed to replace AWS Rekognition dependency |
| TensorFlow Lite | NovaDoor Firmware (C1) — person detection and on-device face recognition | Person detection, on-device face recognition |
| PyTorch | Internal MobileFaceNet model training (in development) | Training infrastructure on EKS |
| AWS Glue / Athena | Data Store (C8) — data cataloguing and query | Over S3 Data Lake |

---

## External SaaS Platforms

| Platform | Purpose | Risk Level |
|---|---|---|
| Auth0 | Identity Provider (OAuth2/OIDC) | High — core auth dependency |
| Stripe | Payment processing | High — revenue-critical |
| AWS Rekognition | Facial recognition (cloud mode) | **Very High** — core product feature dependency; biometric data processing; no abstraction layer |
| Shopify Plus | Hardware e-commerce storefront | Medium — not owned by NovaMesh |
| ShipBob | 3PL fulfilment | Medium — physical supply chain |
| Firebase FCM | Push notifications (mobile) — visitor alerts | Medium |
| SendGrid | Transactional email | Low |
| Twilio | SMS notifications | Low |
| HubSpot | CRM | Medium — customer data |
| Customer.io | Lifecycle email automation | Low |
| Zendesk | Customer support ticketing | Medium |
| Datadog | Observability | Medium |
| PagerDuty | Alerting and on-call | Medium |

---

## Developer Tooling

| Tool | Purpose |
|---|---|
| GitHub | Source control, CI/CD (Actions) |
| Linear | Issue and project tracking |
| Notion | Internal documentation |
| Figma | Design and prototyping |
| Postman | API documentation and testing |
| Docker | Containerisation |
| Helm | Kubernetes package management |

---

## Security

| Technology | Purpose |
|---|---|
| AWS Secrets Manager | Secret and credential storage |
| AWS KMS | Encryption key management — also used for face embedding encryption at rest |
| Auth0 | Authentication, MFA |
| Cloudflare WAF | Web Application Firewall |
| Snyk | Dependency vulnerability scanning |
| AWS GuardDuty | Cloud threat detection |
| TLS 1.3 | All inter-service and device communication |

---

## Notable Gaps

| Gap | Risk |
|---|---|
| No AI provider abstraction layer for face recognition | AWS Rekognition lock-in; a breaking change or price change could take weeks to fix and affects the core product feature |
| No biometric data governance tooling | GDPR / BIPA compliance for face embeddings is manual; no automated classification, lineage, or deletion pipeline |
| No independent ML model update path (edge) | Biometric security patches require a full firmware OTA — weeks of delay for critical model fixes |
| No service mesh (e.g. Istio/Linkerd) | Internal service calls lack mTLS, circuit breaking, and observability |
| No data classification/lineage tooling | Biometric and PII data handling is ad hoc and error-prone |
| No feature flag system | Rollouts are all-or-nothing; no progressive delivery for face recognition models |
| No chaos engineering tooling | Resilience is untested under failure conditions — especially critical for door lock safety |
| No SBOM tooling | Software supply chain visibility is limited |
| No biometric data residency controls | EU customer face data may not be guaranteed to remain within EU |
