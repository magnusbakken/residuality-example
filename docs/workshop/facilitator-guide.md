# Workshop Facilitator Guide — Residuality Theory with NovaMesh

This guide is for the person facilitating the workshop. It describes the full session flow, timing, facilitation tips, and how to handle common failure modes.

---

## Workshop Overview

**Purpose**: Apply Residuality Theory techniques to the NovaMesh smart door architecture to develop a more resilient, uncertainty-aware design.

**Target participants**:
- Software architects (2–4)
- Senior engineers from each domain (3–5)
- Product managers familiar with the NovaMesh product (1–2)
- A business stakeholder (CEO/CTO/CPO — optional but recommended) (0–1)

**Ideal group size**: 6–12 participants. Larger groups should be split into teams of 4–6.

**Duration**: Full-day workshop (5–6 hours with breaks), or two half-day sessions.

**Prerequisites**:
- Participants should read the [Company Profile](../company-profile.md) and [As-Is Architecture Overview](../architecture/as-is-overview.md) **before** the workshop. Send these in advance with a 1-week lead time.
- Facilitator should be familiar with all documentation in this repository.
- Print or prepare digital copies of the Stressors Catalogue and blank incidence matrix for each participant.

---

## Materials Required

| Item | Quantity | Notes |
|---|---|---|
| Printed/digital Company Profile | 1 per person | |
| Printed/digital As-Is Architecture Overview | 1 per person | |
| Printed/digital Component Catalogue | 1 per person | |
| Printed/digital Stressors Catalogue | 1 per person | |
| Blank Incidence Matrix template | 1 per team | See Exercise 2 |
| Sticky notes (3 colours: red, yellow, green) | 2 pads per team | |
| Large whiteboard or Miro/Mural board | 1 per team | |
| Timer visible to room | 1 | |
| 2× six-sided dice (or equivalent randomiser) | 1 set per team | For random stressor generation |

---

## Session Structure

### Pre-Workshop: Preparation (1 week before)

1. Send participants the [Company Profile](../company-profile.md) and [As-Is Architecture Overview](../architecture/as-is-overview.md)
2. Ask them to note 3–5 things that concern them about the architecture from a resilience perspective
3. (Optional) Ask participants: "What is the stressor you fear most for a system like this?" — collect answers anonymously and use them as seed material in Exercise 1

---

### Opening: Scene-Setting (20 minutes)

**Goal**: Ensure all participants share a common understanding of NovaMesh and why we are here.

1. **Welcome and context** (5 min): Introduce the workshop goal — not to solve NovaMesh's problems, but to practice Residuality Theory techniques using it as a vehicle.

2. **Brief company overview** (5 min): Walk through the NovaMesh company profile and key architectural facts. Highlight the NovaDoor product (camera + AI facial recognition + electronic lock), the subscription model, and the "mid-migration" state — some things are live, some are being built, some are planned. Point out that this is a system where architectural decisions have physical-world safety consequences (door locks).

3. **Residuality Theory introduction** (10 min): Present the core concepts (stressors, residues, attractors, criticality). Use the framing from the [Residuality Theory Overview](../residuality-theory/overview.md). The key messages are:
   - We can't predict the future, but we can design systems that survive it
   - We're looking for what *survives* stress, not what avoids it
   - The goal is criticality: existing residues already handle stressors we haven't imagined yet

**Facilitator tip**: Don't let the introduction become a lecture. Ask the group: "What's the biggest assumption baked into this architecture that you'd be uncomfortable with?"

---

### Exercise 1: Stressor Identification (45 minutes)

See [Exercise 1 — Stressor Identification](./exercises/01-stressor-identification.md) for full instructions.

**Summary**: Teams work through the Stressors Catalogue, discuss whether each stressor is credible for NovaMesh, and generate at least 5 new stressors that are not in the catalogue (including at least 1 "implausible" stressor). Teams prioritise their top 10 stressors for use in Exercise 2.

**Facilitator role during this exercise**:
- Circulate between tables; listen for interesting stressors being dismissed as "not relevant"
- Challenge dismissals: "You say that's implausible — but what architectural assumption makes it implausible? Is that assumption safe?"
- Encourage the "implausible" stressor — this is often where the most interesting residue discoveries happen
- Note any stressors that generate significant disagreement for the debrief

**Common failure mode**: Teams spend too long debating the likelihood of stressors. Redirect with: "Residuality Theory deliberately doesn't weight by likelihood. Treat all stressors as equally possible for this exercise."

---

### Exercise 2: Building the Incidence Matrix (60 minutes)

See [Exercise 2 — Incidence Matrix](./exercises/02-incidence-matrix.md) for full instructions.

**Summary**: Using the team's top 10 stressors, teams build their own incidence matrix mapping stressors to components. They calculate row and column sums, identify the most vulnerable components and highest-impact stressors, and look for hyperliminal coupling patterns.

**Facilitator role during this exercise**:
- This is the most cognitively demanding exercise — monitor for fatigue and keep energy up
- Prompt teams to look for indirect impacts (○ marks), not just direct ones
- The most valuable insights come from discovering that a component affects far more things than expected — encourage teams to articulate *why* a stressor affects a component
- Encourage comparison with the pre-built matrix in the documentation — teams often find different patterns

**Common failure mode**: Teams only mark direct/obvious impacts and miss second-order effects. Prompt: "If this stressor happens, what happens to *X*? And then what happens to *Y* as a result?"

**Key prompt**: When a team finds a component with a high column sum: "What is it about this component that makes so many stressors converge on it? Is it a data ownership problem? A vendor dependency? An architectural pattern?"

---

### Exercise 3: Attractor Analysis (45 minutes)

See [Exercise 3 — Attractor Analysis](./exercises/03-attractor-analysis.md) for full instructions.

**Summary**: Teams review the 5 attractors described in the [Attractors Analysis](../residuality-theory/attractors-analysis.md) and discuss which attractors NovaMesh is currently gravitating toward. They identify which stressors from their top-10 list accelerate each attractor, and add any attractors they believe are missing.

**Facilitator role during this exercise**:
- Encourage teams to think of attractors as "gravity wells" — the system doesn't fall into them all at once; it drifts gradually
- Challenge the assumption that attractors are necessarily bad: "Could the Monolith Resurrection attractor actually be the right answer for NovaMesh given their constraints?"
- Surface business-level attractors, not just technical ones: "What happens to the business model if the company settles into this state?"

**Key question to pose**: "If you came back to this architecture in 2 years with no intervention, which attractor would it be in? Why?"

---

### Exercise 4: Residue Redesign (60 minutes)

See [Exercise 4 — Residue Redesign](./exercises/04-residue-redesign.md) for full instructions.

**Summary**: Each team selects the top 3 residue design priorities from their matrix analysis. For each, they design a residue — an architectural change, pattern, abstraction, or capability — that makes the system more resilient to their identified stressors. The residue must address multiple stressors simultaneously (not be a point fix for a single scenario).

**Facilitator role during this exercise**:
- This is the most generative exercise — give teams space to design
- A residue is *not* a specific solution to a specific stressor. Redirect if a team says "we'd use multi-AZ RDS to fix S-ENV-01": "That's a solution. What's the underlying architectural capability that would make the system resilient to the whole category of infrastructure stressors?"
- Push teams toward residues that address multiple stressors (high row/column intersection density in the matrix)
- Encourage residues that acknowledge *uncertainty* — a good residue doesn't require knowing when or whether the stressor will arrive

**Example residue framing**: "Instead of 'add multi-region support,' a residue might be: *an edge-first recognition mode where the NovaDoor performs facial recognition locally and executes auto-unlock rules without requiring cloud connectivity.* This addresses S-ENV-01 (cloud region outage), S-ENV-02 (ISP outage), S-TECH-02 (Rekognition outage), and S-TECH-07 (Auth0 outage) simultaneously — and it also provides a product advantage for privacy-conscious customers (S-REG-04)."

---

### Debrief and Synthesis (30 minutes)

**Structure**:

1. **Gallery walk** (10 min): Each team presents their top 3 residue designs to the room. Aim for 2–3 minutes per residue, 5–6 total residues across teams.

2. **Cross-team synthesis** (10 min): Facilitator highlights patterns across teams:
   - Which stressors did multiple teams independently identify as highest-impact?
   - Which residues did multiple teams design independently?
   - Where did teams disagree significantly?

3. **Criticality test** (10 min): Pose new stressors to the room — ones *not* in the catalogue. Ask: "Does our updated architecture (with the proposed residues) already handle this stressor? If yes, we're moving toward criticality."

**Closing prompt**: Ask each participant to write one sentence on a sticky note: "The architectural assumption I'm most concerned about, that this exercise revealed."

---

## Facilitation Tips

### Managing disagreement

Disagreement about whether a stressor is "relevant" or "plausible" is actually productive data. It reveals different mental models of the system. When it happens:
- Name it explicitly: "We have a genuine disagreement here — let's note this stressor as 'contested' and move on. We'll come back to it."
- At the debrief, revisit contested stressors: disagreements often reveal assumptions that are baked into the architecture

### Handling "but we'd never let that happen"

When participants say "we'd never deploy a firmware update without testing," remind them:
> "Residuality Theory doesn't say you will make this mistake. It asks: if this stressor arrived — through any means — what does the architecture do? Is the outcome a graceful degradation or a catastrophic failure? Good testing is operational hygiene. Residues are architectural resilience."

### Keeping non-technical stakeholders engaged

Business stakeholders sometimes disengage during technical exercises. Keep them anchored with business framing:
- During Exercise 1: "What stressors would most concern you as a CEO/CPO?"
- During Exercise 2: "Which of these components being vulnerable would affect your ability to achieve your company goals?"
- During Exercise 4: "What would need to be true about the architecture for you to confidently tell the board that the system is resilient?"

### Time pressure

If the group is running behind:
- Exercise 1 can be shortened by pre-selecting 10 stressors from the catalogue rather than generating new ones. However, **do not skip the implausible stressor generation** — it is the most unique contribution of Residuality Theory
- Exercise 3 (Attractor Analysis) can be shortened to 25 minutes by skipping the "missing attractors" step
- Exercise 4 can be reduced to 2 residues per team rather than 3

---

## Frequently Asked Questions

**Q: Isn't this just Risk Management with extra steps?**

A: Risk management asks "what are the known risks?" and tries to mitigate them. Residuality Theory asks "what does the architecture do when *any* stress arrives?" The key difference is that residues are designed for *unknown* stressors, not just known ones. The incidence matrix technique surfaces coupling that risk registers miss entirely.

**Q: The stressors don't all have equal probability — shouldn't we weight them?**

A: Deliberately, no. The random simulation approach is a core feature of the theory. Weighting by probability recreates the same blind spots that probability-based risk assessment has always had. An "implausible" stressor that affects 10 components is architecturally more revealing than a "likely" stressor that affects 1.

**Q: How does this relate to Domain-Driven Design / Clean Architecture / etc.?**

A: Residuality Theory is complementary to these approaches. DDD helps you *describe* the architecture (bounded contexts, ubiquitous language). Residuality Theory helps you *evaluate* its resilience to uncertainty. A well-structured DDD architecture may still have high hyperliminal coupling that only the incidence matrix reveals.

**Q: At what point do we have "enough" residues?**

A: The goal is criticality — when it becomes difficult to imagine a new stressor that isn't already addressed by existing residues. In practice, this is a directional goal, not a binary state. The measure of progress is: "Can we now think of stressors that our proposed residues *don't* handle?" If yes, there is more work to do.

---

## Post-Workshop Actions

After the workshop, the facilitator should:

1. Document the stressors generated by teams (especially new ones not in the catalogue)
2. Record the incidence matrices produced by each team
3. Compile the residue designs proposed
4. Schedule a follow-up session (2–4 weeks later) to review:
   - Which residue proposals were accepted into the product/architecture roadmap?
   - Were any new stressors discovered in the intervening weeks that should be added to the catalogue?
   - Has the understanding of any attractor changed?

---

## References for Facilitators

- O'Reilly, B. (2024). *Residues: Time, Change, and Uncertainty in Software Architecture*. LeanPub. https://leanpub.com/residuality
- [Producing a Better Software Architecture with Residuality Theory — InfoQ (2025)](https://www.infoq.com/news/2025/10/architectures-residuality-theory/)
- [Residuality Theory: A Rebellious Take on Building Systems That Actually Survive — Architecture Weekly](https://www.architecture-weekly.com/p/residuality-theory-a-rebellious-take)
- [The Philosophy of Residuality Theory — ScienceDirect (2020)](https://www.sciencedirect.com/science/article/pii/S1877050920305561)
- [Book Notes: Residues — Dan Lebrero (2025)](https://danlebrero.com/2025/08/20/residues-time-uncertainty-change-software-architecture-summary/)
- [NDC Residuality Theory Workshop — Barry O'Reilly](https://ndcworkshops.com/slot/residuality-theory-workshop-oneday/)
- [Embracing Residuality Theory in Software Architecture — Springer (2024)](https://link.springer.com/chapter/10.1007/978-3-031-75201-8_3)
