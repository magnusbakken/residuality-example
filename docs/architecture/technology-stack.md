# Technology Stack

A reference catalogue of all technologies used across the NovaMesh platform.

---

## Cloud & Infrastructure

| Technology | Purpose | Notes |
|---|---|---|
| AWS (primary) | Cloud infrastructure | EKS, ECS, RDS, DynamoDB, S3, MSK, IoT Core, API Gateway, Lambda, Secrets Manager, SQS, SNS |
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
| Go 1.22 | Identity Service, Device Management Service |
| Python 3.12 | AI/ML services, Data Platform, Support Chatbot |
| Python 3.11 / Django 4.2 | Legacy Monolith |
| Node.js 20 | Subscription Service, Notification Service |
| TypeScript / React | Web Application |
| React Native | iOS and Android mobile apps |
| C / Embedded Linux | Hub firmware |

---

## Databases & Storage

| Technology | Used By | Notes |
|---|---|---|
| PostgreSQL 15 | Identity Service, Subscription Service, Monolith, AI Orchestration | RDS managed instances |
| DynamoDB | Device Management Service | NoSQL; hot-partition risk during mass OTA |
| Redis | Notification Service (queue), API Gateway (rate limiting), AI Orchestration (cache) | ElastiCache |
| S3 | Data Lake, firmware artefacts, ML model artefacts | |
| Pinecone | Support Chatbot | Vector database for RAG |
| Snowflake | Data Warehouse | Being set up |

---

## Messaging & Streaming

| Technology | Used For | Notes |
|---|---|---|
| Apache Kafka (AWS MSK) | Device telemetry, business event bus | Core of real-time data platform |
| AWS SNS | Notification triggers between services | Fan-out from service events |
| AWS SQS | Async job queues | Used by several services for background work |
| AWS IoT Core | MQTT broker for Hub device communication | Device → Cloud messaging |
| AWS IoT Device Shadow | Device state synchronisation | Persistent device state store |

---

## AI & Machine Learning

| Technology | Used For | Notes |
|---|---|---|
| OpenAI GPT-4o API | AI Assistant (primary LLM), Support Chatbot | Critical external dependency; no abstraction layer |
| Anthropic Claude API | AI Assistant (fallback) | Partial fallback; not fully implemented |
| OpenAI Embeddings API | Support Chatbot (RAG embeddings) | `text-embedding-3-large` |
| TensorFlow Lite | Edge AI on Hub firmware | Anomaly detection, wake-word models |
| XGBoost | Predictive Maintenance Engine | Gradient boosted trees |
| Scikit-learn | Anomaly Detection Engine (Isolation Forest) | Ensemble with LSTM |
| PyTorch | LSTM component of Anomaly Detection | |
| Pinecone | Vector store for RAG | |
| AWS Glue / Athena | Data cataloguing and query | Over S3 Data Lake |
| Airbyte | ELT from SaaS sources | Shopify, HubSpot, Zendesk → Snowflake |

---

## External SaaS Platforms

| Platform | Purpose | Risk Level |
|---|---|---|
| Auth0 | Identity Provider (OAuth2/OIDC) | High — core auth dependency |
| Stripe | Payment processing | High — revenue-critical |
| Shopify Plus | Hardware e-commerce storefront | Medium — not owned by NovaMesh |
| ShipBob | 3PL fulfilment | Medium — physical supply chain |
| Firebase FCM | Push notifications (mobile) | Medium |
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
| AWS KMS | Encryption key management |
| Auth0 | Authentication, MFA |
| Cloudflare WAF | Web Application Firewall |
| Snyk | Dependency vulnerability scanning |
| AWS GuardDuty | Cloud threat detection |
| TLS 1.3 | All inter-service and device communication |

---

## Notable Gaps

| Gap | Risk |
|---|---|
| No service mesh (e.g. Istio/Linkerd) | Internal service calls lack mTLS, circuit breaking, and observability |
| No AI provider abstraction layer | Vendor lock-in for OpenAI; breaking changes could take weeks to fix |
| No data classification/lineage tooling | GDPR compliance is manual and error-prone |
| No feature flag system | Rollouts are all-or-nothing; no progressive delivery |
| No chaos engineering tooling | Resilience is untested under failure conditions |
| No SBOM tooling | Software supply chain visibility is limited |
