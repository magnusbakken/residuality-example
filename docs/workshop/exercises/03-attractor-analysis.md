# Exercise 3 — Attractor Discussion

**~20 minutes**

---

## Goal

Identify which attractors the NovaMesh architecture is currently drifting toward, and see whether the matrix you built in Exercise 2 confirms, challenges, or extends the pre-built attractor analysis.

---

## What to Do

Read the [Attractors Analysis](../../residuality-theory/attractors-analysis.md) together, or at least the one-line descriptions of the three attractors:

| ID | Name | Short Description |
|---|---|---|
| A1 | Monolith Resurrection | Microservices migration stalls; features flow back into the monolith |
| A2 | Face Recognition Vendor Capture | All facial recognition structurally depends on AWS Rekognition; internal model abandoned; biometric data permanently third-party |
| A3 | Biometric Privacy Collapse | Regulatory enforcement or reputational damage forces data minimisation; recognition accuracy drops; auto-unlock cannot be safely offered |

Then discuss:

**1. Which attractor(s) does the incidence matrix point toward?**

Look at your highest-impact stressors and most vulnerable components. Do they cluster around one of the three attractors? If S-TECH-01 and S-ORG-01 both came in at the same time, what would happen?

**2. Where are you placing your stressors?**

For each stressor in your working set, ask: if this stressor arrived, which attractor would it push the system toward? Some stressors push toward multiple attractors simultaneously — those are worth flagging.

**3. What's missing?**

The three attractors were identified through analysis. Are there drift patterns your matrix reveals that none of them describe? What would you call them?

Some prompts for finding missing attractors:
- "What would happen to NovaMesh if it grew 10x without changing its architecture?"
- "What if a series of false-accept incidents forced NovaMesh to disable auto-unlock company-wide?"
- "What if the founders left and the company was acquired by a PE firm focused on cost extraction?"

**4. Attractors aren't always bad**

Could any of the three attractors actually be a *better* outcome than the current trajectory under some conditions? For example: could Attractor A3 (forced edge-only recognition) actually become a competitive advantage if handled well?

---

## Output

Before moving to Exercise 4:
1. Which attractor is NovaMesh drifting toward most strongly, and why?
2. Which stressors from your working set accelerate which attractors?
3. Any new attractors the group identified
