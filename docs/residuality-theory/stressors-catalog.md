# Stressors Catalogue

This catalogue contains the stressors to be used in the NovaMesh Residuality Theory workshop. They are organised by category but should be treated as a pool for random simulation — the category is an aid to generation, not a constraint on application.

> **Important**: In Residuality Theory, stressors do not need to be likely. Even low-probability or seemingly implausible stressors are valuable — they challenge architectural assumptions and often reveal hidden vulnerabilities that realistic risk analysis would miss. The goal is not a risk register. It is architectural stress simulation.

---

## Stressor Notation

Each stressor has:
- **ID**: Reference identifier for the incidence matrix
- **Name**: Short descriptive name
- **Description**: What happens / what changes
- **Trigger type**: External / Internal / Environmental
- **Time horizon**: Immediate (< 1 day) | Short (days–weeks) | Medium (weeks–months) | Long (months+)
- **Initial impact zone**: Which part of NovaMesh is first affected

---

## Category 1: Regulatory & Legal

### S-REG-01 — AI Transparency Regulation
**Trigger**: External | **Time horizon**: Long  
**Initial impact**: AI & Intelligence Layer (Zone 3)

A new EU AI Act enforcement ruling requires that all AI-generated decisions affecting consumers (e.g., anomaly alerts, account flags, insurance integration data) must include a human-readable explanation of the reasoning, stored and auditable for 5 years. NovaMesh's current models produce no explanations, and no audit log exists.

---

### S-REG-02 — GDPR Enforcement Action
**Trigger**: External | **Time horizon**: Short  
**Initial impact**: Identity Service, Data Platform

A GDPR enforcement agency opens an investigation following a complaint from a user whose deletion request took 47 days to be fully completed. NovaMesh must demonstrate within 72 hours that it can execute a full data deletion across all systems and provide a data map showing every location where user PII is stored.

---

### S-REG-03 — IoT Device Security Mandate
**Trigger**: External | **Time horizon**: Medium  
**Initial impact**: Hub Firmware, Device Management Service

A government IoT cybersecurity regulation (similar to the EU Cyber Resilience Act) mandates that all connected devices shipped after a specific date must support automatic security patching without user intervention, and manufacturers must maintain a Software Bill of Materials (SBOM). NovaMesh currently has a ~3–5% OTA failure rate and no SBOM tooling.

---

### S-REG-04 — Children's Online Privacy Expansion
**Trigger**: External | **Time horizon**: Medium  
**Initial impact**: Identity Service, AI Platform, Data Platform

New regulations expand children's privacy protections to include AI inference on household data. Smart home devices used in households with children require explicit parental consent for each AI feature, with data minimisation requirements. NovaMesh has no concept of household composition or age-based consent in its data model.

---

### S-REG-05 — Financial Services Integration Restriction
**Trigger**: External | **Time horizon**: Long  
**Initial impact**: Subscription Service, AI Platform

A new "contextual data sharing" regulation prohibits selling or sharing any AI-derived home behaviour insights to financial services companies (insurers, lenders) without explicit opt-in and data residency guarantees. A planned NovaMesh revenue stream (selling aggregate insights to insurance partners) is blocked before it launches.

---

## Category 2: Competitive & Market

### S-MKT-01 — Big Tech Launches Competing Product
**Trigger**: External | **Time horizon**: Medium  
**Initial impact**: Marketing, Sales, Subscription churn

Google announces "Nest AI Pro" — a direct competitor with equivalent AI features, Google ecosystem integration, and a price point 40% lower than NovaMesh Premium. Subscription churn spikes to 18% MoM (from baseline 3%). NovaMesh's ISP partnership channel begins shifting to the Google product.

---

### S-MKT-02 — Hardware Supply Chain Disruption
**Trigger**: External | **Time horizon**: Short  
**Initial impact**: E-Commerce, Hardware operations

The sole manufacturer of the custom AI processor used in the NovaMesh Hub announces a 6-month production halt due to factory fire. NovaMesh has 3 weeks of Hub inventory. New hardware orders cannot be fulfilled; the post-purchase subscription activation pipeline dries up.

---

### S-MKT-03 — Subscription Model Commoditisation
**Trigger**: External | **Time horizon**: Long  
**Initial impact**: Subscription Service, Product strategy

Multiple competitors offer free tiers with AI features similar to NovaMesh's paid tiers, funded by advertising or data monetisation. Consumer willingness to pay for NovaMesh's Protect and Insights tiers drops dramatically. ARR growth stalls; the company must pivot pricing strategy within 90 days.

---

### S-MKT-04 — ISP Partnership Termination
**Trigger**: External | **Time horizon**: Medium  
**Initial impact**: Sales channel, Device activations

NovaMesh's largest ISP distribution partner (responsible for 22% of device activations) terminates the partnership, citing a competitive conflict with their own new smart home product. No replacement channel is immediately available.

---

### S-MKT-05 — Enterprise Customer Consolidation
**Trigger**: External | **Time horizon**: Long  
**Initial impact**: Enterprise accounts, Monolith, Sales

Three of NovaMesh's top-10 enterprise customers are acquired by a single large real estate conglomerate. The new parent company mandates a single vendor for all properties and issues an RFP. NovaMesh must respond within 30 days with enterprise features (multi-tenancy, compliance reporting, SSO, SLA guarantees) that are currently only partially implemented.

---

## Category 3: Technical & Infrastructure

### S-TECH-01 — OpenAI API Breaking Change
**Trigger**: External | **Time horizon**: Short  
**Initial impact**: AI Assistant, Support Chatbot, AI Orchestration

OpenAI announces deprecation of the GPT-4o API endpoint used by the AI Assistant, effective in 30 days, replacing it with a new model API that has a different function-calling format. The AI Orchestration Service has no abstraction layer; every AI feature must be individually updated.

---

### S-TECH-02 — OpenAI Outage (Extended)
**Trigger**: External | **Time horizon**: Immediate  
**Initial impact**: AI Assistant, Support Chatbot

OpenAI experiences a 6-hour outage. The AI Assistant returns errors to all users attempting to use it. The Support Chatbot is unavailable. The Anthropic fallback is only partially implemented; it handles text responses but not function calls (device commands). Support tickets spike 400%.

---

### S-TECH-03 — Monolith Database Corruption
**Trigger**: Internal | **Time horizon**: Immediate  
**Initial impact**: Legacy Monolith, Notification Service (preferences), Billing (dual-write)

A failed database migration corrupts 15% of rows in the monolith PostgreSQL database. The monolith becomes partially unavailable. The Subscription Service dual-write begins writing to a corrupted source. The Notification Service loses access to preference data for all users still on the monolith DB.

---

### S-TECH-04 — Kafka Cluster Failure
**Trigger**: Internal | **Time horizon**: Immediate  
**Initial impact**: Anomaly Detection, Data Platform, Device telemetry pipeline

The Kafka (MSK) cluster experiences a broker failure resulting in a 4-hour outage. Device telemetry events are dropped (MSK auto-recovery with potential message loss). The Anomaly Detection Engine stops processing. Real-time alerts are not generated. The Data Lake receives no data for 4 hours, affecting model retraining pipelines.

---

### S-TECH-05 — DynamoDB Hot Partition at Scale
**Trigger**: Internal | **Time horizon**: Short  
**Initial impact**: Device Management Service, OTA campaign

NovaMesh launches a marketing campaign that results in a simultaneous mass activation of 25,000 new Hubs over 2 hours. The DynamoDB hot-partition issue (previously observed in OTA rollouts) causes read/write throttling in the Device Management Service. Device registration succeeds inconsistently; some customers' Hubs appear offline.

---

### S-TECH-06 — Stripe Outage During Billing Cycle
**Trigger**: External | **Time horizon**: Immediate  
**Initial impact**: Subscription Service, Billing

Stripe experiences an outage during the 1st of the month billing cycle — when ~40% of NovaMesh subscriptions renew. Renewal payments fail. Stripe's webhook delivery is delayed by up to 24 hours after recovery. Some subscriptions are incorrectly downgraded during the outage window due to the lack of idempotency in webhook processing.

---

### S-TECH-07 — Auth0 Outage
**Trigger**: External | **Time horizon**: Immediate  
**Initial impact**: Identity Service, All authenticated endpoints

Auth0 experiences a 3-hour global outage. No new authentication tokens can be issued. Users with expired sessions cannot log in. The API Gateway Lambda Authoriser cannot validate JWTs. Effectively, the entire NovaMesh platform is inaccessible to all unauthenticated users and those whose sessions expire during the outage.

---

### S-TECH-08 — OTA Update Introduces Critical Bug
**Trigger**: Internal | **Time horizon**: Immediate  
**Initial impact**: Hub Firmware, Device Management, Customer trust

A firmware update (v2.5.0) contains a bug that causes 30% of deployed Hubs to enter a boot loop after update. Approximately 35,000 Hubs become non-functional. Rollback is manual and requires SSH access to each affected device. The OTA system has no automated rollback capability. Support tickets overwhelm the Zendesk queue.

---

### S-TECH-09 — Edge AI Model Data Poisoning
**Trigger**: External (adversarial) | **Time horizon**: Medium  
**Initial impact**: Edge AI Runtime, Anomaly Detection, Customer trust

A security researcher discloses a vulnerability: adversarial inputs to the edge anomaly detection model can cause it to suppress all alerts on a compromised device. Because ML models can only be updated via firmware OTA, deploying a patched model requires a full firmware release cycle (2–3 weeks minimum).

---

### S-TECH-10 — Data Lake Accidental Deletion
**Trigger**: Internal | **Time horizon**: Immediate  
**Initial impact**: Data Platform, AI model training pipelines

An engineer accidentally runs a deletion command against the production S3 Data Lake bucket with insufficient IAM restrictions. 14 months of device telemetry data is permanently deleted. This data was the primary training set for the Anomaly Detection and Predictive Maintenance models.

---

### S-TECH-11 — Dual-Write Data Divergence (Silent)
**Trigger**: Internal | **Time horizon**: Medium  
**Initial impact**: Subscription Service, Billing integrity

The dual-write mechanism between the Subscription Service and the Monolith DB silently accumulates divergence over 3 months. A downstream billing report reveals that ~1,200 customers have been charged for a subscription tier they cancelled; a further ~800 have active paid features they are not being charged for. The root cause is intermittent network failures causing partial write success.

---

## Category 4: Organisational & People

### S-ORG-01 — Key AI Engineer Resignation
**Trigger**: Internal | **Time horizon**: Short  
**Initial impact**: AI Platform (in-build), Anomaly Detection

The lead ML engineer — the sole person with deep knowledge of the Anomaly Detection model training pipeline and the AI Orchestration Service architecture — resigns to join a competitor. Critical institutional knowledge is not documented. The AI Platform development velocity drops by ~60%.

---

### S-ORG-02 — Rapid Headcount Growth Breakdown
**Trigger**: Internal | **Time horizon**: Medium  
**Initial impact**: Cross-cutting — ownership and operations

NovaMesh doubles engineering headcount from 36 to 72 in 6 months following the Series B. New engineers are onboarded across teams, but ownership of production services becomes unclear. Three separate teams make conflicting changes to the Subscription Service API within the same week, causing a production incident. The lack of defined service ownership and SLOs means nobody is accountable.

---

### S-ORG-03 — CTO Departure
**Trigger**: Internal | **Time horizon**: Long  
**Initial impact**: Technical strategy, Architecture decisions

The CTO (the primary author of the microservices migration strategy and AI Platform architecture) departs the company following a disagreement with the board over the enterprise pivot. Incoming interim CTO wants to pause the monolith migration and focus entirely on enterprise features. This creates strategic whiplash in the engineering organisation.

---

### S-ORG-04 — Firmware Team Specialisation Loss
**Trigger**: Internal | **Time horizon**: Short  
**Initial impact**: Hub Firmware, OTA, Edge AI Runtime

Both senior firmware engineers leave within 2 months of each other. The firmware team is reduced to 3 junior engineers. A critical security patch for the Hub firmware is needed within 4 weeks due to a disclosed kernel vulnerability. The team does not have the expertise to safely deliver the patch.

---

## Category 5: Environmental & Physical

### S-ENV-01 — Cloud Region Outage
**Trigger**: External | **Time horizon**: Immediate  
**Initial impact**: All cloud-hosted services (NovaMesh runs primarily in us-east-1)

AWS us-east-1 experiences a widespread partial outage affecting EKS, RDS, and MSK. All NovaMesh microservices are affected. The Hub devices lose cloud connectivity and fall back to local-only mode (limited functionality). The monolith (ECS) is completely unavailable. No disaster recovery failover exists to another region.

---

### S-ENV-02 — Widespread Internet Outage (ISP-Level)
**Trigger**: External | **Time horizon**: Immediate  
**Initial impact**: Hub devices, Consumer experience

A BGP routing incident causes widespread internet disruption for 4 hours across a major ISP. ~30% of NovaMesh Hubs lose cloud connectivity. Local Wi-Fi and basic home network functionality continues (the Hub can operate as a basic router), but all cloud-dependent AI features, app control, and monitoring are unavailable. Customers experience this as the product "being down."

---

### S-ENV-03 — Physical Hardware Defect Discovery
**Trigger**: Internal | **Time horizon**: Short  
**Initial impact**: Hardware operations, E-Commerce, Customer trust

A hardware defect is discovered in the NovaMesh Hub batch shipped in Q3 2024 (approximately 18,000 units). The defect causes the device to overheat under sustained AI inference load. A product recall may be required. All affected devices must be identified and customers notified within 72 hours.

---

## Category 6: Reputational & Social

### S-REP-01 — Security Breach — Customer Data Leak
**Trigger**: External (adversarial) | **Time horizon**: Immediate  
**Initial impact**: Identity Service, Data Platform, Customer trust, Regulatory

A threat actor exploits a vulnerability in the legacy monolith and exfiltrates a database dump containing email addresses, hashed passwords, home addresses, and device telemetry data for ~85,000 customers. The data appears for sale on a dark web forum. NovaMesh must respond publicly within 72 hours (GDPR breach notification) and manage customer communication.

---

### S-REP-02 — AI False Positive Causes Harm
**Trigger**: Internal | **Time horizon**: Immediate  
**Initial impact**: Anomaly Detection, Customer trust, Regulatory

The Anomaly Detection Engine generates a false positive alert on a customer's network, flagging it as a security threat. The customer, following instructions in the alert, factory-resets their home network — disrupting their home office setup during a critical business deadline. The customer posts a viral social media thread. Media coverage follows, questioning the reliability of AI-driven home security products. NovaMesh's 8% false positive rate (target: 2%) becomes public knowledge.

---

### S-REP-03 — AI Assistant Privacy Violation
**Trigger**: Internal | **Time horizon**: Immediate  
**Initial impact**: AI Assistant, AI Orchestration, Regulatory, Customer trust

A customer discovers that their AI Assistant conversation — which included discussion of sensitive home health monitoring — has been logged in plain text in the AI Orchestration Service's PostgreSQL database. They post about it publicly. Investigation reveals no explicit data retention policy exists for AI conversation data, and all queries are retained indefinitely.

---

### S-REP-04 — Viral Negative Review Campaign
**Trigger**: External | **Time horizon**: Short  
**Initial impact**: Sales, Marketing, Customer Success

An influential tech YouTuber (2M subscribers) publishes a 20-minute negative review of NovaMesh after experiencing the OTA boot loop bug (S-TECH-08). The video receives 800K views. 1-star reviews flood the App Store, Google Play, and Amazon product pages. New hardware sales drop 45% in the following 2 weeks.

---

## Bonus Stressor: The "Unknown Unknown"

### S-UNK-01 — (To be generated in workshop)
**Trigger**: Random | **Time horizon**: Unknown  
**Initial impact**: Unknown

In the workshop, participants will generate one or more additional stressors by rolling dice or using a random trigger prompt. This stressor is intentionally left blank — part of the exercise is to discover that even a randomly generated, implausible stressor reveals something about the architecture.

> *Suggested random triggers: "What if electricity became intermittent in 40% of homes for 6 months?" / "What if LLM inference became 1000x cheaper overnight, making your AI moat disappear?" / "What if home ownership dropped to 30% and most of your customers became renters who move every 6 months?"*
