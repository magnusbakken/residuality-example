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

1. Multi-region / infrastructure resilience capability
2. Monolith decomposition completion (enterprise context)
3. Unified data governance and deletion pipeline
4. AI provider abstraction layer
5. Notification Service preference migration (end the monolith dependency)
6. AI explainability / audit capability
7. Automated OTA rollback mechanism
8. Knowledge management / documentation for AI systems

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
> *"If OpenAI experiences an extended outage (S-TECH-02), the system continues serving AI-assisted home control requests via the Anthropic fallback, with response times increasing by < 500ms. The AI provider abstraction layer residue survives as the interface specification and routing logic — which remains unchanged regardless of which underlying provider is active."*

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
- "What if a critical dependency on a specific database engine (PostgreSQL) became untenable due to licensing changes?"
- "What if a major mobile OS update broke the React Native app's push notification handling permanently?"
- "What if customer behaviour changed so that 90% of interactions moved to voice interfaces exclusively?"
- "What if a new AI architecture paradigm emerged that made all current ML models obsolete within 12 months?"

---

### Step 3 — Residue Gallery (10 minutes)

Each team presents their 3 residue designs in 2–3 minutes each. Use the template structure as a guide.

During presentations, other teams listen for:
- Do any teams' residues address the same stressors but in different ways? What does that divergence reveal?
- Are there residues that address stressors you hadn't considered?
- Does any residue fail the criticality test? (It doesn't address the new stressors posed by the facilitator)

---

## Residue Design Examples

### Example Residue: AI Provider Independence

**Description**: A vendor-agnostic AI capability layer within the NovaMesh platform. All internal AI consumers interact with a common interface that specifies *what* is needed (intent, context, required capabilities) without binding to *how* it is delivered. The implementation can route to any AI provider, local model, or hybrid combination.

**Addresses**:
- S-TECH-01 (OpenAI API breaking change) — only the router implementation changes, not all consumers
- S-TECH-02 (OpenAI extended outage) — automatic fallback to Anthropic or internal models
- S-REG-05 (Financial services data restriction) — data can be routed to on-premise or region-specific providers per regulatory constraint
- S-REP-03 (AI privacy violation) — logging and data retention controlled at the abstraction layer

**Resists**: Attractor A2 (AI Vendor Capture)

**What Survives**: If OpenAI experiences an extended outage, the AI Assistant continues functioning via the Anthropic fallback; the response format is identical; function calling is available through a provider-neutral schema. The residue — the abstraction layer interface and routing logic — survives unchanged.

---

### Example Residue: Edge-First Degraded Mode

**Description**: A capability where the NovaMesh Hub can maintain defined "core functionality" without cloud connectivity for an extended period (target: 72 hours). Core functionality is defined as: local AI inference for anomaly detection, stored automation rules execution, and local network management. Non-core features (AI Assistant, remote access) degrade gracefully with user notification.

**Addresses**:
- S-ENV-01 (Cloud region outage) — devices continue operating in local mode
- S-ENV-02 (ISP outage) — loss of internet does not mean loss of home protection
- S-TECH-07 (Auth0 outage) — local tokens allow continued device-level operation
- S-REG-03 (IoT security mandate) — local security processing meets on-device requirements

**Resists**: Attractor A2 (AI Vendor Capture — reduces reliance on cloud AI), Attractor A5 (Privacy Collapse — processing stays local)

**What Survives**: If internet connectivity is lost for 72 hours, all configured automations continue executing, anomaly detection continues locally, and the customer's home network continues functioning. The residue — the local rule engine and edge AI inference capability — survives independently of the cloud.

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
