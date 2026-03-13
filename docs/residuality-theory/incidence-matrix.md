# Stressor × Residue Incidence Matrix

The incidence matrix is the central analytical tool in Residuality Theory. It maps **stressors** (rows) against **architectural components / residues** (columns), marking which stressors affect which components.

By summing rows and columns, we can identify:
- **High-impact stressors** (high row sum): those that affect many components simultaneously — these are the most dangerous stressors for the architecture
- **Vulnerable components** (high column sum): those affected by many stressors — these are architectural weak points

---

## Component Key

| ID | Component |
|---|---|
| C1 | Identity & User Management Service |
| C2 | Legacy Monolith |
| C3 | Device Management Service |
| C4 | API Gateway |
| C5 | Notification Service |
| C6 | Subscription & Billing Service |
| C7 | E-Commerce (Shopify) |
| C8 | AI Orchestration Service |
| C9 | Anomaly Detection Engine |
| C10 | Predictive Maintenance Engine |
| C11 | AI Assistant |
| C12 | Support Chatbot |
| C13 | Data Platform |
| C14 | Edge AI Runtime (Hub Firmware) |
| C15 | Marketing & CRM |
| C16 | Observability & Operations |

---

## The Matrix

A `●` indicates that the stressor directly or significantly affects the component. A `○` indicates indirect or secondary impact.

| Stressor | C1 | C2 | C3 | C4 | C5 | C6 | C7 | C8 | C9 | C10 | C11 | C12 | C13 | C14 | C15 | C16 | **Row Σ** |
|---|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **S-REG-01** AI Transparency Regulation | | ○ | | | | | | ● | ● | ● | ● | ● | ● | ● | | | **8** |
| **S-REG-02** GDPR Enforcement | ● | ● | ● | | ● | ● | ○ | ● | ○ | | ● | | ● | | ● | | **10** |
| **S-REG-03** IoT Security Mandate | | | ● | | | | | | | | | | | ● | | ○ | **3** |
| **S-REG-04** Children's Privacy | ● | ○ | | | ● | | | ● | ● | | ● | | ● | ○ | | | **7** |
| **S-REG-05** Financial Services Restriction | | | | | | ● | | ● | ● | | | | ● | | ● | | **5** |
| **S-MKT-01** Big Tech Competitor | | ○ | | | | ● | ● | ○ | | | ● | | | | ● | | **5** |
| **S-MKT-02** Supply Chain Disruption | | | ● | | | ○ | ● | | | | | | | ● | ○ | | **5** |
| **S-MKT-03** Subscription Commoditisation | | ○ | | | | ● | ● | | | | | | | | ● | | **4** |
| **S-MKT-04** ISP Partnership Termination | | | ● | | | ○ | ● | | | | | | | ● | ● | | **5** |
| **S-MKT-05** Enterprise Customer Consolidation | ● | ● | ○ | | | ● | | | | | | ○ | | | ○ | | **5** |
| **S-TECH-01** OpenAI API Breaking Change | | | | | | | | ● | | | ● | ● | | | | | **3** |
| **S-TECH-02** OpenAI Outage (Extended) | | | | | ○ | | | ● | | | ● | ● | | | | ● | **5** |
| **S-TECH-03** Monolith DB Corruption | ○ | ● | | ● | ● | ● | | | | | | | ● | | | ● | **8** |
| **S-TECH-04** Kafka Cluster Failure | | | ● | | ● | | | | ● | ● | | | ● | | | ● | **6** |
| **S-TECH-05** DynamoDB Hot Partition | | | ● | ○ | ○ | | | | ● | | | | ● | | | ● | **5** |
| **S-TECH-06** Stripe Outage Billing Cycle | | ○ | | | ● | ● | ○ | | | | | | | | | | **4** |
| **S-TECH-07** Auth0 Outage | ● | | | ● | | | | ● | | | ● | | | | | ● | **5** |
| **S-TECH-08** OTA Bug — Boot Loop | | | ● | | ● | ○ | | | | ● | | ● | | ● | | ● | **7** |
| **S-TECH-09** Edge AI Model Poisoning | | | ● | | ● | | | ● | ● | | | | | ● | | ● | **6** |
| **S-TECH-10** Data Lake Deletion | | | | | | | | | ● | ● | | | ● | | | | **3** |
| **S-TECH-11** Dual-Write Divergence | ○ | ● | | | ● | ● | | | | | | | | | | | **4** |
| **S-ORG-01** Key ML Engineer Resignation | | | | | | | | ● | ● | ● | ● | ● | ○ | | | | **6** |
| **S-ORG-02** Rapid Headcount Growth | ○ | ● | ○ | | | ● | | ○ | ○ | | | | | | | ● | **5** |
| **S-ORG-03** CTO Departure | | ● | | | | ● | | ● | ● | ● | | | | | | | **5** |
| **S-ORG-04** Firmware Team Loss | | | ● | | | | | | | | | | | ● | | | **2** |
| **S-ENV-01** Cloud Region Outage | ● | ● | ● | ● | ● | ● | | ● | ● | | ● | ● | ● | | | ● | **13** |
| **S-ENV-02** ISP-Level Internet Outage | | | ● | | ● | | | ● | ● | | ● | | | ● | | ● | **7** |
| **S-ENV-03** Hardware Defect Discovery | | ○ | ● | | ● | ○ | ● | | | | | ● | | ● | ○ | | **7** |
| **S-REP-01** Customer Data Breach | ● | ● | | ● | | ● | | ● | | | ● | | ● | | ● | ● | **10** |
| **S-REP-02** AI False Positive Harm | | ○ | | | ● | | | ● | ● | | | ● | | | | ● | **6** |
| **S-REP-03** AI Assistant Privacy Violation | ● | ○ | | | | | | ● | | | ● | | ● | | | ● | **6** |
| **S-REP-04** Viral Negative Review | | ● | ○ | | ● | ○ | ● | | | | | ● | | | ● | | **6** |
| **Column Σ** | **10** | **17** | **16** | **5** | **17** | **16** | **8** | **17** | **15** | **7** | **14** | **10** | **13** | **9** | **8** | **12** | |

---

## Analysis: High-Impact Stressors (Row Sums ≥ 6)

The following stressors affect 6 or more components and represent the most architecturally dangerous scenarios:

| Stressor | Row Σ | Key Observation |
|---|---|---|
| **S-ENV-01** Cloud Region Outage | **13** | Highest impact stressor. NovaMesh has no multi-region strategy. A single region failure affects virtually every service |
| **S-REG-02** GDPR Enforcement | **10** | Second highest. GDPR enforcement touches every domain where PII is stored or processed — which is nearly everywhere |
| **S-REP-01** Customer Data Breach | **10** | Equal to GDPR. A breach cascades across identity, billing, AI data, and compliance simultaneously |
| **S-REG-01** AI Transparency Regulation | **8** | AI explainability affects every AI component and the data platform. None of the current AI components produce explainable outputs |
| **S-TECH-03** Monolith DB Corruption | **8** | Despite the monolith being "migrated," its database is still shared by multiple services |
| **S-TECH-08** OTA Bug — Boot Loop | **7** | Physical device failure cascades into support, notification, and data collection |
| **S-REG-04** Children's Privacy | **7** | Cross-cutting privacy requirement with no current data model support |
| **S-ENV-02** ISP-Level Internet Outage | **7** | Hub devices go offline; edge AI cannot compensate for all cloud dependencies |
| **S-ENV-03** Hardware Defect Discovery | **7** | Physical recall cascades into digital operations |
| **S-ORG-01** Key ML Engineer Resignation | **6** | Organisational knowledge is concentrated; no documentation of AI components |
| **S-TECH-04** Kafka Cluster Failure | **6** | Real-time data backbone — affects anomaly detection, data platform, and notifications |
| **S-TECH-09** Edge AI Model Poisoning | **6** | Security attack on AI models propagates through device management and notifications |
| **S-ORG-02** Rapid Headcount Growth | **6** | People stressor with systemic architecture impact |
| **S-REP-02** AI False Positive Harm | **6** | Trust erosion cascades into support and AI operations |
| **S-REP-03** AI Assistant Privacy Violation | **6** | Privacy cascades into regulatory and reputational damage |
| **S-REP-04** Viral Negative Review | **6** | External trust impact cascades into sales and support |

---

## Analysis: Vulnerable Components (Column Sums ≥ 10)

The following components are affected by 10 or more stressors and represent architectural vulnerability hotspots:

| Component | Column Σ | Key Observation |
|---|---|---|
| **C2** Legacy Monolith | **17** | Highest vulnerability score. Despite being "migrating," it is affected by more stressors than any other component. This is the clearest signal that the monolith decomposition is architecturally critical |
| **C5** Notification Service | **17** | Equal to the Monolith. The Notification Service's hidden coupling to the monolith DB explains much of this — it inherits monolith vulnerabilities while appearing to be a separate service |
| **C6** Subscription & Billing Service | **16** | The dual-write state makes it doubly vulnerable — it inherits stressors from both its own domain and from the monolith |
| **C8** AI Orchestration Service | **17** | Despite being only 40% built, it has the same vulnerability score as the monolith. This is because it is the planned single point through which all AI features flow — concentration risk |
| **C3** Device Management Service | **16** | Hub and IoT operations are affected by both technical and environmental/regulatory stressors |
| **C9** Anomaly Detection Engine | **15** | The AI component with the most exposure, reflecting its dependency on telemetry data, AI providers, and regulatory requirements around AI decisions |
| **C11** AI Assistant | **14** | High vendor dependency (OpenAI) and privacy risk concentrate stressors here |
| **C13** Data Platform | **13** | Cross-cutting data concerns mean almost every regulatory and data stressor reaches this component |
| **C16** Observability | **12** | Observability appears in many stressor responses — the ability to detect and respond to stressors depends on it |
| **C1** Identity Service | **10** | Authentication is a foundational concern; privacy and breach stressors all touch it |
| **C12** Support Chatbot | **10** | Front-line customer experience component; privacy, AI, and reputational stressors all converge here |

---

## Hyperliminal Coupling Discovery

The incidence matrix reveals **hyperliminal coupling** — components that appear independent but are linked through shared stressor response. These are not visible in the component diagram but emerge from the matrix analysis:

### Coupling 1: Notification Service ↔ Legacy Monolith (via Preferences DB)

- **S-TECH-03** (Monolith DB Corruption) affects both C2 and C5 directly
- **S-ENV-01** (Region Outage) affects both C2 and C5 because C5 reads from the monolith DB
- **S-TECH-11** (Dual-Write Divergence) affects both C2 and C5

The Notification Service shares a failure mode with the Monolith even though they are nominally separate services. The matrix column overlap reveals this link that the component diagram doesn't show.

### Coupling 2: AI Orchestration Service ↔ All AI Components

The AI Orchestration Service (C8) has the same column sum as the Legacy Monolith (17). This reveals that the planned AI architecture has recreated the Monolith's concentration risk in a different domain. All AI stressors pass through C8 — which is only 40% built and has no SLO, no circuit breakers, and no production hardening.

### Coupling 3: Subscription Service ↔ Monolith (via Dual-Write)

The Subscription Service (C6) appears in stressors that affect the Monolith even when the primary failure is in the monolith. The dual-write mechanism creates bidirectional coupling: monolith failures corrupt billing state, and billing failures can corrupt monolith state.

### Coupling 4: GDPR Cross-Domain Chain

**S-REG-02** affects 10 components. This reveals a pattern: any regulatory enforcement action around data privacy creates a crisis that simultaneously touches Identity (C1), Billing (C6), AI (C8, C9, C11), Notifications (C5), Data Platform (C13), and CRM (C15). No single team owns the GDPR response; it requires coordination across every domain simultaneously — a classic hyperliminal coupling through regulatory specification.

---

## Residue Design Priorities (Derived from Matrix)

The matrix suggests the following residues should be prioritised (highest impact/vulnerability):

| Priority | Residue | Addresses |
|---|---|---|
| 1 | **Multi-region failover capability** | S-ENV-01 (row Σ = 13); affects 13 components |
| 2 | **Monolith decomposition completion** | C2 (col Σ = 17); highest vulnerability component |
| 3 | **Unified data governance & deletion pipeline** | S-REG-02 (row Σ = 10); S-REP-01 (row Σ = 10) |
| 4 | **AI provider abstraction layer** | C8 concentration risk (col Σ = 17); S-TECH-01, S-TECH-02 |
| 5 | **Notification Service preference migration** | Hyperliminal coupling with Monolith |
| 6 | **AI explainability capability** | S-REG-01 (row Σ = 8); affects all AI components |
| 7 | **Automated OTA rollback** | S-TECH-08 (row Σ = 7); S-REG-03 |
| 8 | **Knowledge management for AI systems** | S-ORG-01 (row Σ = 6) — people risk |
| 9 | **Idempotent webhook processing** | S-TECH-06; S-TECH-11 |
| 10 | **Edge-only degraded mode** | S-ENV-02 (row Σ = 7) — internet outage resilience |
