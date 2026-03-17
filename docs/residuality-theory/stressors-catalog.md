# Stressors Catalogue

This catalogue contains example stressors for the NovaMesh Residuality Theory workshop. They are a starting point — not an exhaustive list. Part of the workshop is generating your own.

> **Important**: In Residuality Theory, stressors do not need to be likely. Even low-probability or seemingly implausible stressors are valuable — they challenge architectural assumptions and often reveal hidden vulnerabilities that realistic risk analysis would miss. The goal is not a risk register. It is architectural stress simulation.

---

## Stressor Notation

Each stressor has:
- **ID**: Reference identifier for the incidence matrix
- **Name**: Short descriptive name
- **Trigger type**: External / Internal / Environmental
- **Time horizon**: Immediate (< 1 day) | Short (days–weeks) | Medium (weeks–months) | Long (months+)
- **Initial impact zone**: Which part of NovaMesh is first affected

---

## Example Stressors

### S-TECH-01 — AWS Rekognition API Breaking Change
**Trigger**: External | **Time horizon**: Short
**Initial impact**: Facial Recognition Service

AWS announces deprecation of the Rekognition API versions used by the Facial Recognition Service, effective in 45 days, with an incompatible new API. The Facial Recognition Service has no abstraction layer — it calls Rekognition directly. Without Rekognition, cloud-mode face recognition is unavailable for all Free and Protect tier customers. The team must patch every call site individually.

---

### S-REG-02 — GDPR Enforcement Action (Biometric Data)
**Trigger**: External | **Time horizon**: Short
**Initial impact**: Identity Service, Data Store, Facial Recognition Service

A European data protection authority opens an investigation following a complaint from an EU customer whose face data deletion request went unfulfilled after 60 days. The authority demands NovaMesh provide, within 72 hours: (1) a complete map of all locations where the complainant's biometric data is stored, (2) evidence of deletion from every location, and (3) a data processing agreement with AWS Rekognition as a data processor. NovaMesh cannot currently satisfy any of these three requirements without extensive manual work.

---

### S-ENV-01 — Cloud Region Outage
**Trigger**: External | **Time horizon**: Immediate
**Initial impact**: All cloud-hosted services (NovaMesh runs primarily in us-east-1)

AWS us-east-1 experiences a widespread partial outage affecting EKS, RDS, IoT Core, and MSK. All NovaMesh microservices are affected. NovaDoor devices lose cloud connectivity. Face recognition (cloud mode) stops working. Lock commands from the app cannot be delivered. Visitor alerts are not sent. The monolith is completely unavailable. Devices running in privacy mode (edge recognition, Insights/Premium tier) continue to auto-unlock for enrolled faces — but no app notification is sent. No disaster recovery failover exists to another region.

---

### S-ORG-01 — Key AI Engineer Resignation
**Trigger**: Internal | **Time horizon**: Short
**Initial impact**: Facial Recognition Service, Access Rules Engine

The lead ML engineer — the sole person with deep knowledge of the MobileFaceNet model training pipeline, the face embedding schema, and the recognition service architecture — resigns to join a competitor. Critical knowledge about recognition accuracy tuning and the biometric data deletion process is not documented. The internal model development stalls indefinitely. The team is now fully dependent on AWS Rekognition with no clear path to reducing that dependency.

---

### S-REP-02 — False Face Recognition Causes Physical Harm
**Trigger**: Internal | **Time horizon**: Immediate
**Initial impact**: Facial Recognition Service, Access Rules Engine

The Facial Recognition Service generates a false accept: a stranger's face is matched with confidence 81% to an enrolled household member ("Grandma"). The auto-unlock rule fires; the door unlocks. The stranger enters the home. The resident, who was alerted "Grandma is at your door," opens the interior door to find a stranger inside. The incident is reported to police and media. Investigation reveals the false accept rate of ~2% — combined with 115,000 active devices — means this type of event is statistically expected to occur multiple times per week across the customer base.

---

## Generating Your Own

The catalogue above is intentionally small. In the workshop, you'll likely want to add stressors that feel more relevant to your actual experience. Some prompts to get started:

- **Technical**: "What dependency in this architecture is everyone assuming will always work?"
- **Regulatory**: "What biometric or privacy regulation could emerge in the next 2 years that would hit hardest?"
- **Competitive**: "What could Ring or Google do that NovaMesh currently has no answer to?"
- **Organisational**: "What people or knowledge situation would most cripple NovaMesh's ability to operate safely?"
- **Environmental**: "What physical-world event would make the fact that this product controls a door lock most dangerous?"
- **Reputational**: "What could go wrong with facial recognition that would be hardest to explain to a court?"

### The Implausible Stressor

Residuality Theory specifically encourages stressors that feel unlikely. These often reveal the most interesting architectural assumptions. A few prompts:

- "What if face recognition accuracy suddenly dropped to 40% for all customers due to a model update error?"
- "What if AI liability legislation made software companies responsible for all physical harms caused by their AI features?"
- "What if a zero-day in the Linux kernel used by NovaDoor firmware was exploited across all IoT devices globally?"
- "What if a court ruling required all enrolled face embeddings to be stored only on the device itself, with no cloud copy?"
