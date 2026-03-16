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

### S-REG-01 — Biometric Data Regulation (Federal)
**Trigger**: External | **Time horizon**: Long
**Initial impact**: Facial Recognition Engine, Data Platform, Identity Service

A new US federal biometric privacy law is passed, modelled on Illinois BIPA but with federal enforcement. It requires explicit written consent before collecting or storing any biometric identifier (including face embeddings), a published biometric data retention policy, and the right to deletion within 30 days. NovaMesh currently has no explicit consent workflow at enrollment, stores embeddings indefinitely, and has no automated deletion pipeline. Class action exposure is estimated at $1,000–$5,000 per violation per person.

---

### S-REG-02 — GDPR Enforcement Action (Biometric Data)
**Trigger**: External | **Time horizon**: Short
**Initial impact**: Identity Service, Data Platform, Facial Recognition Engine

A European data protection authority opens an investigation following a complaint from an EU customer whose face data deletion request went unfulfilled after 60 days. The authority demands NovaMesh provide, within 72 hours: (1) a complete map of all locations where the complainant's biometric data is stored, (2) evidence of deletion from every location, and (3) a data processing agreement with AWS Rekognition as a data processor. NovaMesh cannot currently satisfy any of these three requirements without extensive manual work.

---

### S-REG-03 — IoT Device Security Mandate
**Trigger**: External | **Time horizon**: Medium
**Initial impact**: NovaDoor Firmware, Device Management Service

A government IoT cybersecurity regulation mandates that all connected devices with cameras shipped after a specific date must: support automatic security patching without user intervention, encrypt all video at rest and in transit, and maintain a Software Bill of Materials (SBOM). NovaMesh currently has a ~3–5% OTA failure rate, no SBOM tooling, and a model update path tied to full firmware OTA — meaning a biometric model vulnerability cannot be patched independently of the firmware.

---

### S-REG-04 — Facial Recognition Prohibition (Municipal/State)
**Trigger**: External | **Time horizon**: Medium
**Initial impact**: Facial Recognition Engine, Subscription Service, Marketing

Several US cities and one US state where NovaMesh has high customer density pass legislation prohibiting the use of automated facial recognition in residential settings without law enforcement authorisation. This would functionally eliminate NovaMesh's core paid feature (Protect/Insights/Premium tiers) for affected customers. NovaMesh has no geo-fencing capability in its AI features; turning off face recognition for specific users requires manual intervention.

---

### S-REG-05 — Insurance Partner Data Sharing Restriction
**Trigger**: External | **Time horizon**: Long
**Initial impact**: Subscription Service, Data Platform, AI Platform

A regulatory ruling prohibits sharing AI-derived residential access data (who enters and exits a home, at what times) with insurance companies without explicit per-claim opt-in and data residency guarantees. A planned NovaMesh revenue stream — selling aggregate visitor intelligence to home insurance partners — is blocked before it launches. Additionally, an existing pilot with an insurance partner must be unwound, requiring deletion of shared data.

---

## Category 2: Competitive & Market

### S-MKT-01 — Ring Launches AI Face Recognition Feature
**Trigger**: External | **Time horizon**: Medium
**Initial impact**: Marketing, Sales, Subscription churn

Amazon Ring announces that all Ring Video Doorbell Pro devices will receive free facial recognition ("Familiar Faces") powered by Amazon's Rekognition service, bundled with Prime membership. NovaMesh's core Protect tier differentiator — face recognition at $8.99/month — is now available for free to Ring/Prime subscribers. Subscription churn spikes to 22% MoM (from baseline 3%). New hardware sales drop 38%.

---

### S-MKT-02 — Camera Component Supply Chain Disruption
**Trigger**: External | **Time horizon**: Short
**Initial impact**: E-Commerce, Hardware operations

The sole supplier of the 4K HDR camera sensor used in the NovaDoor announces a 5-month production halt due to a factory fire in Taiwan. NovaMesh has 3 weeks of NovaDoor inventory. New hardware orders cannot be fulfilled; the post-purchase subscription activation pipeline dries up. No alternative camera sensor with equivalent performance is currently qualified for the NovaDoor enclosure.

---

### S-MKT-03 — Subscription Model Commoditisation
**Trigger**: External | **Time horizon**: Long
**Initial impact**: Subscription Service, Product strategy

Eufy and Arlo announce that all AI camera features — including basic person recognition and package detection — are now free with hardware purchase, funded by hardware margin improvement. Consumer willingness to pay for NovaMesh's Protect and Insights tiers drops dramatically. ARR growth stalls; the company must either restructure pricing or find new revenue streams within 90 days.

---

### S-MKT-04 — Amazon Smart Home Platform Access Restriction
**Trigger**: External | **Time horizon**: Medium
**Initial impact**: Third-party integrations, Sales channel, Device activations

Amazon restricts Alexa integration access to door lock and camera devices to companies that meet new certification requirements, including data sharing agreements that would require NovaMesh to route face recognition results through Amazon's cloud. NovaMesh cannot comply with the data sharing terms (biometric data sharing with Amazon). The Alexa integration goes dark. NovaMesh's Amazon sales channel (responsible for ~28% of hardware sales) begins to underperform.

---

### S-MKT-05 — Property Management Consolidation
**Trigger**: External | **Time horizon**: Long
**Initial impact**: Enterprise accounts, Monolith, Sales

Three of NovaMesh's top-10 enterprise customers (property management companies) are acquired by a single large REIT. The new parent mandates a single vendor for access control across all properties and issues an RFP. NovaMesh must respond within 30 days with enterprise features (multi-site management, compliance reporting, SSO, visitor management, SLA guarantees, data residency documentation) that are currently only partially implemented and largely still inside the monolith.

---

## Category 3: Technical & Infrastructure

### S-TECH-01 — AWS Rekognition API Breaking Change
**Trigger**: External | **Time horizon**: Short
**Initial impact**: Facial Recognition Engine, AI Orchestration Service

AWS announces deprecation of the Rekognition `CompareFaces` and `SearchFacesByImage` API versions used by the Facial Recognition Engine, effective in 45 days, replacing them with a new API with different request/response schemas and a new authentication model. The AI Orchestration Service has no abstraction layer; every downstream component that consumes recognition results must be individually updated. Without Rekognition, cloud-mode face recognition is unavailable — affecting all Free and Protect tier customers.

---

### S-TECH-02 — Cloud Recognition Service Outage
**Trigger**: External | **Time horizon**: Immediate
**Initial impact**: Facial Recognition Engine, Notification Service, Access Rules Engine

AWS Rekognition experiences a 5-hour regional outage. All cloud-mode face recognition returns errors. Visitors arrive at NovaDoors: devices detect motion and persons, but face recognition fails. Customers receive "Unknown visitor" alerts even for enrolled household members. Auto-unlock rules that depend on face recognition stop firing — residents cannot auto-enter their homes. Support tickets spike 500%. The edge-mode fallback exists only for Insights/Premium tier customers.

---

### S-TECH-03 — Monolith Database Corruption
**Trigger**: Internal | **Time horizon**: Immediate
**Initial impact**: Legacy Monolith, Notification Service (preferences), Billing (dual-write)

A failed database migration corrupts 15% of rows in the monolith PostgreSQL database. The monolith becomes partially unavailable. The Subscription Service dual-write begins writing to a corrupted source. The Notification Service loses access to preference data for all users still on the monolith DB, reverting to default notification behaviour (potentially sending alerts to opted-out channels or failing to send to opted-in channels).

---

### S-TECH-04 — Kafka Cluster Failure
**Trigger**: Internal | **Time horizon**: Immediate
**Initial impact**: Facial Recognition Engine, Data Platform, visitor event pipeline

The Kafka (MSK) cluster experiences a broker failure resulting in a 4-hour outage. Visitor event messages from NovaDoor devices (face frames, motion events) are dropped. The Facial Recognition Engine stops processing. Access rules based on face recognition cannot fire — auto-unlock stops working. The Data Platform receives no visitor events for 4 hours, affecting model training pipelines and recognition audit logs.

---

### S-TECH-05 — DynamoDB Hot Partition at Scale
**Trigger**: Internal | **Time horizon**: Short
**Initial impact**: Device Management Service, OTA campaign

NovaMesh launches a Black Friday promotion resulting in simultaneous activation of 30,000 new NovaDoor units over 3 hours. The DynamoDB hot-partition issue (previously observed in OTA rollouts) causes read/write throttling in the Device Management Service. Device registration succeeds inconsistently; some customers' NovaDoors appear offline in the app and cannot receive lock commands or send visitor events.

---

### S-TECH-06 — Stripe Outage During Billing Cycle
**Trigger**: External | **Time horizon**: Immediate
**Initial impact**: Subscription Service, Billing

Stripe experiences an outage during the 1st of the month billing cycle — when ~40% of NovaMesh subscriptions renew. Renewal payments fail. Some subscriptions are incorrectly downgraded during the outage window due to the lack of idempotency in webhook processing. Customers on the Protect tier (who have auto-unlock configured) find that their access rules stop working because their tier was erroneously downgraded.

---

### S-TECH-07 — Auth0 Outage
**Trigger**: External | **Time horizon**: Immediate
**Initial impact**: Identity Service, all authenticated endpoints

Auth0 experiences a 3-hour global outage. No new authentication tokens can be issued. Users with expired sessions cannot log in to the app. The API Gateway Lambda Authoriser cannot validate JWTs. Effectively, the entire NovaMesh platform is inaccessible — users cannot view live camera, receive alerts, or remotely unlock doors via the app. (Note: doors already configured with auto-unlock rules continue to function via edge AI if privacy mode is enabled.)

---

### S-TECH-08 — OTA Update Introduces Door Lock Bug
**Trigger**: Internal | **Time horizon**: Immediate
**Initial impact**: NovaDoor Firmware, Device Management, Customer trust, Physical safety

A firmware update (v2.5.0) contains a bug that causes 30% of deployed NovaDoors to fail to actuate the electronic lock after update. Approximately 35,000 doors are either stuck locked (residents locked out) or stuck unlocked (homes unsecured). Rollback is manual and requires SSH access to each affected device. Support tickets overwhelm the Zendesk queue. Local media cover the "smart door leaves family locked out" story. The physical safety dimension makes this a severity 1 incident with legal exposure.

---

### S-TECH-09 — Face Recognition Model Poisoning
**Trigger**: External (adversarial) | **Time horizon**: Medium
**Initial impact**: Edge AI Runtime, Facial Recognition Engine, Customer trust

A security researcher discloses a vulnerability: adversarial inputs (subtly modified face images during the enrollment process) can cause the NovaMesh face recognition model to create a "ghost embedding" — a planted face that the system will subsequently recognise and auto-unlock for, even though it was never legitimately enrolled. Because edge ML models can only be updated via firmware OTA, deploying a patched model requires a full firmware release cycle (2–3 weeks minimum). During this window, an attacker who knows the exploit can potentially bypass door access controls.

---

### S-TECH-10 — Face Embedding Store Accidental Deletion
**Trigger**: Internal | **Time horizon**: Immediate
**Initial impact**: Data Platform, Facial Recognition Engine, all enrolled households

An engineer accidentally runs a deletion command against the production `novamesh-faces-db` PostgreSQL database. All enrolled face embeddings for all ~65,000 Protect/Insights/Premium households are permanently deleted. Face recognition immediately fails for all customers — every visitor becomes "Unknown." All auto-unlock rules stop working. Re-enrollment requires every household member to manually re-enroll via the app. Biometric data cannot be recovered from the Rekognition collection without triggering GDPR/BIPA issues around data retention.

---

### S-TECH-11 — Dual-Write Data Divergence (Silent)
**Trigger**: Internal | **Time horizon**: Medium
**Initial impact**: Subscription Service, Billing integrity, Feature entitlement

The dual-write mechanism between the Subscription Service and the Monolith DB silently accumulates divergence over 3 months. A downstream billing report reveals that ~1,200 customers are being billed for a subscription tier they cancelled; a further ~800 have active paid features (including face recognition and auto-unlock) without being charged. The root cause is intermittent network failures causing partial write success.

---

## Category 4: Organisational & People

### S-ORG-01 — Key AI Engineer Resignation
**Trigger**: Internal | **Time horizon**: Short
**Initial impact**: AI Platform (in-build), Facial Recognition Engine

The lead ML engineer — the sole person with deep knowledge of the MobileFaceNet model training pipeline, the face embedding schema, and the AI Orchestration Service architecture — resigns to join a competitor. Critical institutional knowledge about recognition accuracy tuning and the biometric data deletion process is not documented. The AI Platform development velocity drops by ~60%. The ongoing effort to build the internal recognition model (to replace AWS Rekognition) stalls indefinitely.

---

### S-ORG-02 — Rapid Headcount Growth Breakdown
**Trigger**: Internal | **Time horizon**: Medium
**Initial impact**: Cross-cutting — ownership and operations

NovaMesh doubles engineering headcount from 36 to 72 in 6 months following the Series B. New engineers are onboarded across teams, but ownership of production services becomes unclear. Three separate teams make conflicting changes to the Access Rules Engine API within the same week, causing a production incident where some households' auto-unlock rules stopped firing. The lack of defined service ownership and SLOs means nobody is accountable for the recognition pipeline end-to-end latency.

---

### S-ORG-03 — CTO Departure
**Trigger**: Internal | **Time horizon**: Long
**Initial impact**: Technical strategy, Architecture decisions

The CTO (the primary author of the microservices migration strategy and AI Platform architecture) departs the company following a disagreement with the board over the enterprise pivot. The incoming interim CTO wants to pause the internal face recognition model development and the monolith migration, focusing entirely on enterprise access control features. This creates strategic whiplash: the team building the privacy-preserving edge recognition model is told to stop, deepening AWS Rekognition dependency indefinitely.

---

### S-ORG-04 — Firmware Team Specialisation Loss
**Trigger**: Internal | **Time horizon**: Short
**Initial impact**: NovaDoor Firmware, OTA, Edge AI Runtime

Both senior firmware engineers leave within 2 months of each other. The firmware team is reduced to 3 junior engineers. A critical security patch for the NovaDoor firmware is needed within 4 weeks due to a disclosed kernel vulnerability affecting the embedded Linux OS. The team does not have the expertise to safely deliver and validate the patch — particularly the interaction between the kernel update and the electronic lock driver.

---

## Category 5: Environmental & Physical

### S-ENV-01 — Cloud Region Outage
**Trigger**: External | **Time horizon**: Immediate
**Initial impact**: All cloud-hosted services (NovaMesh runs primarily in us-east-1)

AWS us-east-1 experiences a widespread partial outage affecting EKS, RDS, IoT Core, and MSK. All NovaMesh microservices are affected. NovaDoor devices lose cloud connectivity. Face recognition (cloud mode) stops working. Lock commands from the app cannot be delivered. Visitor alerts are not sent. The monolith (ECS) is completely unavailable. Devices running in privacy mode (edge recognition) continue to auto-unlock for enrolled faces — but no app notification is sent. No disaster recovery failover exists to another region.

---

### S-ENV-02 — Widespread Internet Outage (ISP-Level)
**Trigger**: External | **Time horizon**: Immediate
**Initial impact**: NovaDoor devices, Consumer experience, lock commands

A BGP routing incident causes widespread internet disruption for 6 hours across a major ISP. ~35% of NovaDoor devices lose cloud connectivity. Cloud-mode face recognition fails; auto-unlock rules dependent on cloud recognition stop working. Residents cannot remotely unlock doors via the app. Devices with edge-mode recognition (Insights/Premium) continue to auto-unlock locally — but NovaMesh support cannot determine which customers are protected and which are not. Customers experience this as "the door doesn't recognise me."

---

### S-ENV-03 — Hardware Defect Discovery
**Trigger**: Internal | **Time horizon**: Short
**Initial impact**: Hardware operations, E-Commerce, Customer trust

A hardware defect is discovered in the NovaDoor batch shipped in Q3 2024 (approximately 18,000 units). The defect causes the electronic lock to intermittently fail to disengage under sustained temperature load (above 35°C). A product recall may be required. All affected devices must be identified and customers notified within 72 hours. The defect creates both a safety issue (residents locked out in high heat) and a liability issue.

---

## Category 6: Reputational & Social

### S-REP-01 — Biometric Data Breach
**Trigger**: External (adversarial) | **Time horizon**: Immediate
**Initial impact**: Identity Service, Data Platform, Facial Recognition Engine, Customer trust, Regulatory

A threat actor exploits a vulnerability in the `novamesh-faces-db` database (exposed via a misconfigured internal API) and exfiltrates face embeddings for ~65,000 enrolled household members. Unlike a password breach, face embeddings cannot be reset — a person's face is permanent. The data appears on a dark web forum. NovaMesh must respond publicly within 72 hours (GDPR breach notification). The biometric nature of the breach triggers BIPA class action exposure estimated at $300M+. Several states' attorneys general open investigations simultaneously.

---

### S-REP-02 — False Face Recognition Causes Physical Harm
**Trigger**: Internal | **Time horizon**: Immediate
**Initial impact**: Facial Recognition Engine, Access Rules Engine, Customer trust, Regulatory

The Facial Recognition Engine generates a false accept: a stranger's face is matched with confidence 81% to an enrolled household member ("Grandma"). The auto-unlock rule fires; the door unlocks. The stranger enters the home. The resident, who was alerted "Grandma is at your door," opens the interior door to find a stranger inside. The incident is reported to police and media. A viral social media post questioning AI door security follows. Investigation reveals the false accept rate of ~2% — combined with 115,000 devices — means this type of event is statistically expected to occur multiple times per week across the customer base.

---

### S-REP-03 — Unauthorised Surveillance Discovery
**Trigger**: Internal | **Time horizon**: Immediate
**Initial impact**: Data Platform, Facial Recognition Engine, AI Orchestration, Regulatory, Customer trust

A customer discovers that their household's face frames — captured by their NovaDoor over 14 months — have been retained in the NovaMesh S3 Data Lake and used as training data for the facial recognition model, without explicit opt-in consent. They post about it publicly. Investigation reveals that the enrollment flow only asked for consent to "store and use face data to identify visitors" — not for model training. The distinction is legally significant under BIPA. Regulatory complaint filed. Other customers begin checking their data export requests and find similar frame retention.

---

### S-REP-04 — Viral Negative Review Campaign
**Trigger**: External | **Time horizon**: Short
**Initial impact**: Sales, Marketing, Customer Success

An influential tech YouTuber (2.5M subscribers) publishes a 25-minute investigation after the OTA boot loop bug (S-TECH-08) locks their family out of their home for 4 hours. The video ("I trusted a smart door and it locked me out") receives 1.2M views in 48 hours. 1-star reviews flood the App Store, Google Play, and Amazon product pages, referencing both the lock bug and concerns about facial recognition privacy. New hardware sales drop 52% in the following 2 weeks. The story is picked up by major tech press.

---

## Bonus Stressor: The "Unknown Unknown"

### S-UNK-01 — (To be generated in workshop)
**Trigger**: Random | **Time horizon**: Unknown
**Initial impact**: Unknown

In the workshop, participants will generate one or more additional stressors by rolling dice or using a random trigger prompt. This stressor is intentionally left blank — part of the exercise is to discover that even a randomly generated, implausible stressor reveals something about the architecture.

> *Suggested random triggers: "What if face recognition accuracy suddenly dropped to 40% for all customers due to a model update error?" / "What if a court ruling required all biometric data to be stored only in the user's country of residence?" / "What if a competitor launched a $29 one-time-payment smart door camera that eliminated the subscription market?" / "What if electricity fluctuations in a major region caused 8,000 NovaDoors to permanently brick their lock mechanisms?"*
