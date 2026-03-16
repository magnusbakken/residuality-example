# Exercise 4 — Residue Redesign

**Duration**: 60 minutes  
**Group size**: 4–6 per team  
**Materials**: Team outputs from Exercises 2 & 3, blank design canvas (whiteboard / Miro)

---

## Goal

Design **residues** — architectural capabilities, patterns, or structural changes that make the NovaMesh system more resilient to the high-impact stressors and high-drift attractors identified in Exercises 2 and 3.

A residue is not a point fix for a single stressor. A residue is an architectural investment that allows the system to *adapt* when stressors arrive — even stressors that were not explicitly considered when the residue was designed.

---

## What Is a Residue? (Reminder)

> "A residue is a unit of change: an architectural element designed not around a fixed requirement, but around its ability to survive and adapt when stress arrives."

Compare these two approaches to the same problem:

**Solution thinking** (not a residue):
> "Add multi-AZ RDS to reduce the risk of database failure in S-ENV-01."

**Residue thinking**:
> "An *infrastructure-location independence* capability — where every stateful component can be relocated to an alternate cloud region within 4 hours with automated data replication. This addresses S-ENV-01 (region outage), S-TECH-03 (database corruption requiring restore from another region), S-TECH-06 (payment processor outage where failover reduces blast radius), and S-REP-01 (data breach requiring forensic isolation of a region)."

A residue:
- Addresses **multiple stressors** simultaneously (not a single point fix)
- Acknowledges **uncertainty** — it doesn't require knowing *when* or *whether* the stressor will arrive
- Is an **architectural capability**, not a specific implementation
- Can be validated against stressors that were not considered when it was designed

---

## The Residue Design Template

For each residue, use the following template:

```
RESIDUE: [Name]
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
DESCRIPTION:
  What is this architectural capability or structural change?
  (2–4 sentences; describe what it IS, not how to implement it)

ADDRESSES STRESSORS:
  [List stressor IDs from the team's working set]
  For each: briefly explain HOW this residue makes the system
  more resilient to this stressor

RESISTS ATTRACTORS:
  [List attractor IDs from Exercise 3]
  For each: briefly explain HOW this residue prevents drift toward
  this attractor state

WHAT SURVIVES (The Residue):
  If [stressor X] arrives, the system [does what?]
  The component [continues to / gracefully degrades to / adapts by]...

IMPLEMENTATION SIGNALS:
  What concrete architectural decisions or patterns would instantiate
  this residue? (3–5 bullets — patterns, not specific technologies)

TRADE-OFFS:
  What does adopting this residue cost, delay, or make harder?
  What assumptions does it make that might not hold?

CRITICALITY TEST:
  After implementing this residue, apply 2–3 new stressors not in the
  working set. Does the residue already handle them? If yes, we're
  moving toward criticality.
```

---

## Instructions

### Step 1 — Select 3 Residue Targets (10 minutes)

From the outputs of Exercises 2 and 3, identify the 3 highest-priority residue targets. These should be the intersections of:
- **High row sum** in the incidence matrix (high-impact stressors)
- **High column sum** (vulnerable components)
- **High attractor drift score** (attractors the system is moving toward)

The pre-built [Incidence Matrix Analysis](../../residuality-theory/incidence-matrix.md#residue-design-priorities-derived-from-matrix) suggests the following prioritised residues (your team may have a different prioritisation based on your stressor selection):

1. Multi-region / infrastructure resilience capability (with door lock failover behaviour)
2. Biometric data governance pipeline (consent, deletion, residency)
3. AI provider abstraction layer for facial recognition (escape Rekognition lock-in)
4. Edge-first recognition mode (auto-unlock without cloud dependency)
5. Independent ML model update path (decouple face recognition model from firmware OTA)
6. Monolith decomposition completion (notification preferences + enterprise accounts)
7. Recognition pipeline end-to-end SLO (C9 → C11 → C3 chain with safety guarantees)
8. Biometric consent and geo-fencing capability (respond to regulatory prohibition)

**Your team's top 3 may differ** — use your own incidence matrix and attractor analysis outputs.

---

### Step 2 — Design the Residues (40 minutes)

For each of your 3 selected residue targets, complete the Residue Design Template.

Allocate approximately 12–13 minutes per residue.

**Facilitation guidance for each residue**:

#### During "Description"

Challenge the team to describe *capabilities*, not *solutions*:
- "Instead of 'use Kubernetes readiness probes', describe the capability: *a system that can detect and isolate unhealthy instances without manual intervention*"
- "Instead of 'migrate notification preferences out of the monolith', describe the capability: *notification preferences are owned by a single service that can be independently deployed and updated*"

#### During "Addresses Stressors"

The team should be able to articulate *specifically how* the residue makes the system more resilient to each stressor. If they can't explain it concretely, the residue may be too vague or the connection may not exist.

Challenge: "For this stressor to be addressed by this residue, what would actually happen differently when the stressor arrives?"

#### During "What Survives"

This is the core Residuality Theory test. Complete the sentence:

> *"If [stressor] arrives, the system [verb: continues / degrades to / recovers by / adapts to] [describe observable behaviour]. The residue survives as [describe what persists through the stress]."*

Example:
> *"If AWS Rekognition experiences an outage (S-TECH-02), all Insights/Premium tier customers continue receiving accurate visitor identification via on-device face recognition. Free/Protect tier customers receive 'Unknown visitor' alerts until cloud recognition is restored. The edge recognition residue survives as the on-device model, inference pipeline, and local auto-unlock rule execution — which remain functional independent of cloud availability."*

#### During "Trade-offs"

Force the team to be honest about costs. Good residues always involve trade-offs:
- Complexity vs. resilience
- Speed of delivery vs. robustness
- Cost vs. redundancy
- Developer familiarity vs. architectural correctness

If a team can't identify trade-offs, they haven't thought about the residue critically enough.

#### During "Criticality Test"

The facilitator provides 2 new stressors not in the team's working set. Ask: does this residue already handle them?

Suggested new stressors for the criticality test:
- "What if a court ruling required all enrolled face embeddings to be stored only on the device itself, with no cloud copy?"
- "What if a major mobile OS update broke the React Native app's push notification handling — meaning visitor alerts stop reaching homeowners?"
- "What if a security researcher published a tool that could extract face embeddings from video clips and reverse-engineer the identities of enrolled people?"
- "What if NovaMesh's insurance partner demanded that all auto-unlock events be audited in real-time by a third party?"

---

### Step 3 — Residue Gallery (10 minutes)

Each team presents their 3 residue designs in 2–3 minutes each. Use the template structure as a guide.

During presentations, other teams listen for:
- Do any teams' residues address the same stressors but in different ways? What does that divergence reveal?
- Are there residues that address stressors you hadn't considered?
- Does any residue fail the criticality test? (It doesn't address the new stressors posed by the facilitator)

---

## Residue Design Examples

### Example Residue: Face Recognition Provider Independence

**Description**: A vendor-agnostic facial recognition capability layer within the NovaMesh platform. All internal consumers (Access Rules Engine, Notification Service) interact with a recognition interface that specifies *what* is needed (face embedding, match result, confidence score) without binding to *how* it is delivered. The implementation can route to AWS Rekognition (external), the internal MobileFaceNet cloud model, or the on-device edge model based on availability, tier entitlement, and regulatory constraints.

**Addresses**:
- S-TECH-01 (Rekognition API breaking change) — only the router implementation changes, not all consumers
- S-TECH-02 (Rekognition extended outage) — automatic fallback to internal model or edge model
- S-REG-04 (Facial recognition prohibition) — geo-routing can disable cloud recognition for specific jurisdictions
- S-REP-03 (Unauthorised surveillance) — data processing location controlled at the abstraction layer

**Resists**: Attractor A2 (Face Recognition Vendor Capture), Attractor A5 (Biometric Privacy Collapse — gives pathway to edge-only mode without rebuilding consumers)

**What Survives**: If AWS Rekognition experiences an outage, the Facial Recognition Engine routes cloud requests to the internal model; auto-unlock rules continue firing for Protect tier customers via cloud fallback, and for Insights/Premium customers via edge. The residue — the recognition interface specification and routing logic — survives unchanged regardless of which provider is active.

---

### Example Residue: Edge-First Recognition Mode

**Description**: A capability where the NovaDoor can perform facial recognition and execute auto-unlock rules entirely on-device, without requiring cloud connectivity. This is not just a "degraded mode" — it is the primary recognition path for Insights/Premium tier customers, with cloud recognition used only for model improvement feedback and complex cases. The on-device model, rule store, and lock control are self-sufficient for the core product use case.

**Addresses**:
- S-ENV-01 (Cloud region outage) — auto-unlock continues working locally
- S-ENV-02 (ISP outage) — loss of internet does not mean loss of door recognition
- S-TECH-02 (Rekognition outage) — core recognition path is unaffected
- S-TECH-07 (Auth0 outage) — door continues operating without authentication round-trips
- S-REG-04 (Facial recognition prohibition) — edge-only mode processes no biometric data in the cloud

**Resists**: Attractor A2 (Face Recognition Vendor Capture — reduces structural dependency on any cloud provider), Attractor A5 (Biometric Privacy Collapse — privacy-by-design positioning as product strength)

**What Survives**: If internet connectivity is lost indefinitely, enrolled household members can still auto-unlock their door, motion detection continues, and local access rules execute. The residue — the on-device recognition model, cached rule store, and local lock control — survives independently of the cloud. Non-core features (app notifications, remote unlock, visitor history sync) degrade gracefully with user notification.

---

## Reflection Questions

1. Which of the 3 residues your team designed would be the most *surprising* to the NovaMesh engineering team — something they probably haven't thought of in this way?

2. Are there residues that would only be valuable *after* a stressor has arrived (reactive resilience) versus residues that prevent attractors from forming (proactive resilience)? Which are more valuable?

3. The criticality test asks whether the residue handles stressors it wasn't designed for. Did any of your residues pass this test unexpectedly? What does that tell you about the nature of the residue?

4. If NovaMesh could invest in only *one* of the residues from all teams in the room, which would you advocate for? Why?

---

## Next Steps (Post-Workshop)

For each residue design, the workshop output becomes a **residue proposal** that can be:
- Added to the NovaMesh architecture roadmap as a structured architectural requirement
- Used to evaluate existing architectural proposals ("does this satisfy the residue we identified?")
- Revisited in 3 months to assess whether the stressors have arrived and how the residue performed
- Updated as new stressors are discovered

The goal is not to implement all residues immediately — it is to have a prioritised set of architectural investments that are explicitly designed to improve resilience under uncertainty.
