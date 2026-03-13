# Exercise 1 — Stressor Identification

**Duration**: 45 minutes  
**Group size**: 4–6 per team  
**Materials**: Stressors Catalogue, sticky notes (red), whiteboard / Miro board

---

## Goal

Generate a working set of stressors for the NovaMesh architecture that will be used in Exercise 2 (Incidence Matrix). The set should include:
- Stressors from the catalogue that the team considers relevant
- New stressors the team generates that are not in the catalogue
- At least one "implausible" stressor — something that feels unlikely but is technically conceivable

---

## Background Reading

Before starting, ensure the team has reviewed:
- [Company Profile](../../company-profile.md)
- [As-Is Architecture Overview](../../architecture/as-is-overview.md)
- [Stressors Catalogue](../../residuality-theory/stressors-catalog.md)

---

## Instructions

### Part A — Catalogue Review (15 minutes)

1. **Individually** (5 minutes): Read through the Stressors Catalogue. On a red sticky note, write the ID of any stressor that you believe would have a **significant impact** on the NovaMesh architecture. Place these on the whiteboard.

2. **As a team** (10 minutes): Review the sticky notes. For each stressor represented:
   - Briefly discuss *why* it would be impactful
   - Mark it as "High" (affects multiple systems), "Medium" (affects one or two systems), or "Contested" (team disagrees)

---

### Part B — Stressor Generation (20 minutes)

Residuality Theory advocates for **random simulation** of stressors — not just the ones that risk analysis would naturally surface. This part pushes beyond the obvious.

#### Step 1 — Domain Walk (10 minutes)

Work through the six stressor categories and generate **at least 2 new stressors per category** that are not in the catalogue:

| Category | Prompt |
|---|---|
| Regulatory & Legal | "What regulation could be created in the next 3 years that would hit NovaMesh hardest?" |
| Competitive & Market | "What could a competitor do that NovaMesh currently has no answer to?" |
| Technical & Infrastructure | "What technical dependency is everyone in this room assuming will always work?" |
| Organisational & People | "What people or knowledge situation would most cripple NovaMesh's engineering capability?" |
| Environmental & Physical | "What physical-world event would have the most unpredictable effect on this system?" |
| Reputational & Social | "What could go wrong with AI that would be hardest to explain to a journalist?" |

Write each new stressor on a red sticky note. It doesn't need to be fully elaborated — a name and a one-sentence description is enough.

#### Step 2 — The Implausible Stressor (10 minutes)

Roll two dice. Use the result to select a trigger from the table below, and generate a stressor based on it. The stressor should be *technically conceivable* but does not need to be likely.

| Dice Total | Trigger |
|---|---|
| 2 | "All home broadband speeds drop to 1Mbps globally due to a submarine cable incident" |
| 3 | "A key programming language NovaMesh uses is abandoned by its foundation" |
| 4 | "Home ownership in NovaMesh's target market drops by 30% due to economic crisis" |
| 5 | "A competing AI model becomes free and more capable than everything NovaMesh uses" |
| 6 | "NovaMesh's primary cloud provider (AWS) is acquired and announces a 3x price increase" |
| 7 | "A widely-shared academic paper proves that NovaMesh's anomaly detection AI is biased against certain household types" |
| 8 | "The home networking hardware supply chain moves entirely to a single country that imposes export controls" |
| 9 | "A widespread power grid instability affects 20% of homes — intermittent power becomes the norm for 2 months" |
| 10 | "AI liability legislation makes software companies responsible for all harms caused by their AI features" |
| 11 | "A zero-day in the Linux kernel used by NovaMesh Hub firmware is exploited across all IoT devices globally" |
| 12 | "NovaMesh's Series B investors demand a 90-day pivot to a completely different market segment" |

Write the implausible stressor on a **yellow** sticky note to distinguish it from the others.

---

### Part C — Prioritisation (10 minutes)

The team should now have a mix of catalogue stressors and newly generated stressors on the board. Your goal for Exercise 2 is a working set of **10 stressors**.

Using dot voting (each person has 3 votes):
- Vote for the stressors you believe are *most architecturally revealing* — not necessarily the most likely
- The 10 stressors with the most votes become the working set for Exercise 2
- **Rule**: The working set must include at least one stressor from each of the 6 categories
- **Rule**: The working set must include the implausible stressor generated in Part B

Write the final 10 stressors with short names in the team's working area. These are your inputs to Exercise 2.

---

## Discussion Prompts

After prioritisation, spend 5 minutes on:

1. Did any stressor get rejected by consensus that surprised you? Why?
2. Are there categories where the team struggled to generate stressors? What does that tell you?
3. Which stressor generated the most disagreement? Why might different people see this stressor differently?

---

## Output for Exercise 2

A clearly labelled list of 10 stressors (IDs + short names) that the team will use to build the incidence matrix.
