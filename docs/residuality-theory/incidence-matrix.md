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
| C9 | Facial Recognition Engine |
| C10 | Visitor Intelligence Engine |
| C11 | Access Rules Engine |
| C12 | Support Chatbot |
| C13 | Data Platform (incl. Face Embedding Store, Video Storage) |
| C14 | Edge AI Runtime (NovaDoor Firmware) |
| C15 | Marketing & CRM |
| C16 | Observability & Operations |

---

## The Matrix

A `●` indicates that the stressor directly or significantly affects the component. A `○` indicates indirect or secondary impact.

| Stressor | C1 | C2 | C3 | C4 | C5 | C6 | C7 | C8 | C9 | C10 | C11 | C12 | C13 | C14 | C15 | C16 | **Row Σ** |
|---|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **S-REG-01** Biometric Data Regulation | ● | ○ | | | | ○ | | ● | ● | ○ | ● | | ● | ● | ○ | | **8** |
| **S-REG-02** GDPR Enforcement (Biometric) | ● | ● | ○ | | ● | ● | | ● | ● | | ● | | ● | ○ | ● | | **11** |
| **S-REG-03** IoT Security Mandate | | | ● | | | | | | | | | | | ● | | ○ | **3** |
| **S-REG-04** Facial Recognition Prohibition | ● | ○ | | | ● | ● | | ● | ● | | ● | | ● | ○ | ○ | | **9** |
| **S-REG-05** Insurance Partner Restriction | | | | | | ● | | ● | ● | ● | | | ● | | ● | | **6** |
| **S-MKT-01** Ring Launches Face Recognition | | ○ | | | | ● | ● | ○ | ● | | ● | | | | ● | | **6** |
| **S-MKT-02** Camera Supply Chain Disruption | | | ● | | | ○ | ● | | | | | | | ● | ○ | | **4** |
| **S-MKT-03** Subscription Commoditisation | | ○ | | | | ● | ● | | ● | | ● | | | | ● | | **6** |
| **S-MKT-04** Amazon Platform Restriction | | | ● | ○ | ● | ○ | ● | | | | | | | ● | ● | | **6** |
| **S-MKT-05** Property Management Consolidation | ● | ● | ○ | | | ● | | | | | ○ | ○ | | | ○ | | **5** |
| **S-TECH-01** Rekognition API Breaking Change | | | | | | | | ● | ● | | ● | | | ○ | | | **4** |
| **S-TECH-02** Cloud Recognition Outage | | | | | ● | | | ● | ● | | ● | ● | | ○ | | ● | **6** |
| **S-TECH-03** Monolith DB Corruption | ○ | ● | | ● | ● | ● | | | | | | | ● | | | ● | **7** |
| **S-TECH-04** Kafka Cluster Failure | | | ● | | ● | | | | ● | ● | ● | | ● | | | ● | **7** |
| **S-TECH-05** DynamoDB Hot Partition | | | ● | ○ | ○ | | | | ● | | ● | | ● | | | ● | **5** |
| **S-TECH-06** Stripe Outage Billing Cycle | | ○ | | | ● | ● | ○ | | | | ● | | | | | | **4** |
| **S-TECH-07** Auth0 Outage | ● | | | ● | | | | ● | ● | | ● | | | | | ● | **6** |
| **S-TECH-08** OTA Bug — Door Lock Failure | | | ● | | ● | ○ | | | | ● | ● | ● | | ● | | ● | **8** |
| **S-TECH-09** Face Recognition Model Poisoning | | | ● | | ● | | | ● | ● | | ● | | | ● | | ● | **7** |
| **S-TECH-10** Face Embedding Store Deletion | ○ | | | | ● | | | | ● | | ● | ● | ● | ○ | | ● | **7** |
| **S-TECH-11** Dual-Write Divergence | ○ | ● | | | ● | ● | | | | | ● | | | | | | **5** |
| **S-ORG-01** Key AI Engineer Resignation | | | | | | | | ● | ● | ● | ● | ● | ○ | ● | | | **7** |
| **S-ORG-02** Rapid Headcount Growth | ○ | ● | ○ | | | ● | | ○ | ○ | | ● | | | | | ● | **5** |
| **S-ORG-03** CTO Departure | | ● | | | | ● | | ● | ● | ● | ● | | | ○ | | | **7** |
| **S-ORG-04** Firmware Team Loss | | | ● | | | | | | | | | | | ● | | | **2** |
| **S-ENV-01** Cloud Region Outage | ● | ● | ● | ● | ● | ● | | ● | ● | | ● | ● | ● | ○ | | ● | **13** |
| **S-ENV-02** ISP-Level Internet Outage | | | ● | | ● | | | ● | ● | | ● | | | ● | | ● | **7** |
| **S-ENV-03** Hardware Defect Discovery | | ○ | ● | | ● | ○ | ● | | | | | ● | | ● | ○ | | **6** |
| **S-REP-01** Biometric Data Breach | ● | ● | | ● | | ● | | ● | ● | | ● | | ● | | ● | ● | **11** |
| **S-REP-02** False Recognition Causes Harm | | ○ | | | ● | | | ● | ● | | ● | ● | | ○ | | ● | **7** |
| **S-REP-03** Unauthorised Surveillance | ● | ○ | | | | | | ● | ● | | | | ● | | | ● | **6** |
| **S-REP-04** Viral Negative Review | | ● | ○ | | ● | ○ | ● | | | | | ● | | | ● | | **6** |
| **Column Σ** | **11** | **16** | **14** | **5** | **19** | **17** | **8** | **18** | **22** | **5** | **21** | **8** | **14** | **12** | **8** | **13** | |

---

## Analysis: High-Impact Stressors (Row Sums ≥ 6)

The following stressors affect 6 or more components and represent the most architecturally dangerous scenarios:

| Stressor | Row Σ | Key Observation |
|---|---|---|
| **S-ENV-01** Cloud Region Outage | **13** | Highest impact stressor. NovaMesh has no multi-region strategy. A single region failure affects virtually every service — including face recognition and door lock commands |
| **S-REG-02** GDPR Enforcement (Biometric) | **11** | Biometric GDPR enforcement is uniquely severe: it touches identity, billing, recognition, audit logs, video storage, CRM, and the monolith simultaneously. No single team can respond alone |
| **S-REP-01** Biometric Data Breach | **11** | A breach of face embeddings is permanent (faces cannot be changed). Cascades across identity, AI, billing, and regulatory simultaneously |
| **S-REG-04** Facial Recognition Prohibition | **9** | A geo-targeted feature ban would require NovaMesh to selectively disable its core paid feature — capability it doesn't currently have |
| **S-REG-01** Biometric Data Regulation (Federal) | **8** | Consent, retention, and deletion requirements span the full AI pipeline — none of these are currently implemented |
| **S-TECH-08** OTA Bug — Door Lock Failure | **8** | Physical safety dimension elevates this above typical OTA bugs; 35,000 doors in unknown state creates immediate legal and safety exposure |
| **S-TECH-03** Monolith DB Corruption | **7** | Despite migration, the monolith DB still underlies notification preferences and legacy subscriptions |
| **S-TECH-04** Kafka Cluster Failure | **7** | Real-time visitor event backbone — Kafka failure means no recognition, no auto-unlock, no alerts |
| **S-TECH-09** Face Model Poisoning | **7** | Security attack on face recognition has physical access implications; slow remediation due to model-in-firmware constraint |
| **S-TECH-10** Face Embedding Store Deletion | **7** | All enrolled households lose recognition capability instantly; re-enrollment burden on all customers |
| **S-ORG-01** Key AI Engineer Resignation | **7** | Knowledge concentration in facial recognition pipeline is extreme; no documentation |
| **S-ORG-03** CTO Departure | **7** | Strategic shift away from internal model could permanently lock in Rekognition dependency |
| **S-REP-02** False Recognition Causes Harm | **7** | Physical safety consequence; false accept rate problem is structural, not a one-off |
| **S-ENV-02** ISP-Level Internet Outage | **7** | Door recognition and lock commands depend on cloud; edge-mode fallback exists only for subset of customers |
| **S-TECH-02** Cloud Recognition Outage | **6** | Core product feature unavailable; support spikes; access rules stop firing |
| **S-TECH-07** Auth0 Outage | **6** | Platform inaccessible; but edge-mode auto-unlock continues working (an important asymmetry) |
| **S-REG-05** Insurance Partner Restriction | **6** | Blocks a planned revenue stream; requires data deletion from partner systems |
| **S-MKT-01** Ring Launches Face Recognition | **6** | Eliminates price premium for core feature; structural competitive threat |
| **S-MKT-03** Subscription Commoditisation | **6** | Free competitor features undermine subscription revenue model |
| **S-MKT-04** Amazon Platform Restriction | **6** | Loses sales channel + Alexa integration simultaneously |
| **S-ENV-03** Hardware Defect Discovery | **6** | Physical recall with digital notification and fleet management burden |
| **S-REP-03** Unauthorised Surveillance | **6** | Frame retention for training without explicit consent; BIPA class action risk |
| **S-REP-04** Viral Negative Review | **6** | Trust erosion cascades into sales and support |

---

## Analysis: Vulnerable Components (Column Sums ≥ 10)

The following components are affected by 10 or more stressors and represent architectural vulnerability hotspots:

| Component | Column Σ | Key Observation |
|---|---|---|
| **C9** Facial Recognition Engine | **22** | Highest vulnerability score by far. The core product differentiator is also the most architecturally exposed component — affected by regulatory, competitive, technical, organisational, and reputational stressors simultaneously |
| **C11** Access Rules Engine | **21** | Real-time rule evaluation sits at the intersection of recognition results, door commands, and billing entitlements — making it vulnerable to failures anywhere in that chain |
| **C8** AI Orchestration Service | **18** | Routes all AI requests; only 40% built; no SLO; single point of failure for all AI features |
| **C6** Subscription & Billing Service | **17** | Feature entitlement gating for face recognition means billing failures directly disable the product |
| **C5** Notification Service | **19** | Visitor alerts are the primary consumer experience; the hidden monolith DB dependency explains much of this score |
| **C2** Legacy Monolith | **16** | Despite being "migrating," still underlies enterprise accounts, notification preferences, and billing dual-write |
| **C3** Device Management Service | **14** | Hub of all physical door commands; IoT operations affected by both technical and environmental stressors |
| **C13** Data Platform | **14** | Houses face embeddings, video, and recognition audit logs — cross-cutting regulatory target |
| **C16** Observability | **13** | Appears in many stressor responses — ability to detect and respond to stressors depends on it |
| **C14** Edge AI Runtime | **12** | Only defence against cloud outages (via edge recognition) — but model update path is blocked by firmware coupling |
| **C1** Identity Service | **11** | Authentication and household management are foundational; biometric and breach stressors all touch it |

---

## Hyperliminal Coupling Discovery

The incidence matrix reveals **hyperliminal coupling** — components that appear independent but are linked through shared stressor response. These are not visible in the component diagram but emerge from the matrix analysis:

### Coupling 1: Notification Service ↔ Legacy Monolith (via Preferences DB)

- **S-TECH-03** (Monolith DB Corruption) affects both C2 and C5 directly
- **S-ENV-01** (Region Outage) affects both C2 and C5
- **S-TECH-11** (Dual-Write Divergence) affects both C2 and C5

The Notification Service delivers the primary consumer value (real-time visitor alerts) but shares a failure mode with the Monolith through the notification preferences database. A monolith failure means alerts may fire to wrong channels or not at all.

### Coupling 2: Facial Recognition Engine ↔ Access Rules Engine ↔ Device Management (The Recognition-Action Chain)

C9 → C11 → C3 is the critical path for the product's core safety behaviour (auto-unlock). All three components must be working for auto-unlock to function. But they are affected by different stressors:
- C9 is affected by model/AI stressors (S-TECH-01, S-TECH-09)
- C11 is affected by billing/entitlement stressors (S-TECH-06, S-TECH-11)
- C3 is affected by infrastructure/IoT stressors (S-TECH-05, S-ENV-01)

A resident relying on auto-unlock to enter their home could find it fails for any of these independently occurring reasons — with no indication which part of the chain has failed.

### Coupling 3: AWS Rekognition ↔ All AI Consumer Features

The AI Orchestration Service (C8) has the column sum of 18 — approaching the Monolith's score. This reveals that the planned AI architecture has recreated the Monolith's concentration risk in a different domain. C8 routes all AI requests through AWS Rekognition (the current external dependency) with no abstraction layer. All consumer AI stressors pass through C8 and C9 simultaneously.

### Coupling 4: Biometric Data Cross-Domain Chain

**S-REG-02** and **S-REP-01** each affect 11 components. This reveals a critical pattern: any regulatory enforcement or breach involving biometric data creates a crisis that simultaneously touches Identity (C1), Billing (C6), AI (C8, C9, C11), Notifications (C5), Data Platform (C13), and Marketing (C15). No single team owns the biometric data response; it requires coordination across every domain simultaneously — and the response is further complicated by biometric data being held externally at AWS Rekognition.

---

## Residue Design Priorities (Derived from Matrix)

The matrix suggests the following residues should be prioritised (highest impact/vulnerability):

| Priority | Residue | Addresses |
|---|---|---|
| 1 | **Multi-region failover capability** | S-ENV-01 (row Σ = 13); affects 13 components; door lock command path |
| 2 | **Biometric data governance pipeline** (consent, deletion, residency) | S-REG-02 (row Σ = 11); S-REP-01 (row Σ = 11); S-REG-01 (row Σ = 8) |
| 3 | **AI provider abstraction layer for face recognition** | C8/C9 concentration risk; S-TECH-01; S-TECH-02 |
| 4 | **Edge-first degraded mode** (auto-unlock when cloud unavailable) | S-ENV-02 (row Σ = 7); S-TECH-02; S-ENV-01 |
| 5 | **Independent ML model update path** (decouple model from firmware OTA) | S-TECH-09 (row Σ = 7); S-REG-03 |
| 6 | **Monolith decomposition completion** (especially notification prefs + enterprise) | C2 (col Σ = 16); hyperliminal coupling with C5 |
| 7 | **Recognition pipeline end-to-end SLO** (C9 → C11 → C3 chain) | S-TECH-02; S-TECH-04; physical safety dimension |
| 8 | **Biometric consent and geo-fencing capability** | S-REG-04 (row Σ = 9); S-REP-03 (row Σ = 6) |
| 9 | **Knowledge management for AI/biometric systems** | S-ORG-01 (row Σ = 7) — people risk |
| 10 | **Automated OTA rollback with lock-state safety check** | S-TECH-08 (row Σ = 8); S-REG-03 |
