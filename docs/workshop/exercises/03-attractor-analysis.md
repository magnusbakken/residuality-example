# Exercise 3 — Attractor Analysis

**Duration**: 45 minutes  
**Group size**: 4–6 per team  
**Materials**: Attractors Analysis document, team's matrix output from Exercise 2

---

## Goal

Identify which attractors the NovaMesh architecture is currently gravitating toward, understand which stressors accelerate each attractor, and explore whether any attractors are missing from the pre-built set.

---

## Background

In Residuality Theory, **attractors** are the stable states that a complex system gravitates toward when subject to accumulated stress. Rather than collapsing to a single stable state, complex systems can have multiple attractors — and a system can be moving toward more than one simultaneously.

For software architecture:
- An attractor is a *systemic drift* — not a single event, but a direction the architecture moves in as stressors accumulate and decisions compound
- Attractors are not necessarily failures. A system in an attractor state can be functioning — just not in the way originally intended
- Understanding attractors helps architects design residues that resist unhealthy attractors and stabilise around healthy ones

Read the [Attractors Analysis](../../residuality-theory/attractors-analysis.md) document before starting this exercise.

---

## The NovaMesh Attractors (Pre-Built)

| ID | Name | Short Description |
|---|---|---|
| A1 | Monolith Resurrection | Microservices migration stalls; features flow back into the monolith |
| A2 | AI Vendor Capture | All AI capabilities become structurally dependent on a single external vendor |
| A3 | Enterprise Monoculture | Enterprise revenue dominates; consumer product stagnates; AI training data degrades |
| A4 | Fragmented Microservices Swamp | 40+ services with unclear ownership, no governance, cascading complexity |
| A5 | Privacy Collapse | Regulatory enforcement forces data minimisation; AI capabilities are degraded |

---

## Instructions

### Part A — Current State Assessment (15 minutes)

For each attractor, the team votes on one question:

**"On a scale of 1–5, how strongly is the NovaMesh architecture currently drifting toward this attractor?"**

1 = No evidence of drift / actively resisted  
3 = Some drift visible; could go either way  
5 = Strong drift; architectural decisions are compounding toward this state

Use a simple show of hands or dot-voting. Record the team's average score for each attractor.

Then discuss (2–3 minutes per high-scoring attractor):
- "What specific evidence from the current architecture supports this drift?"
- "Is there any architectural decision or constraint that is *actively resisting* this attractor? Or is the system drifting unchecked?"

---

### Part B — Stressor-to-Attractor Mapping (15 minutes)

Take the team's **top 10 stressors** from Exercise 1.

For each stressor, discuss: **"Which attractor(s) does this stressor accelerate, if it arrives?"**

Place each stressor ID in one or more attractor "zones" on the whiteboard. Look for:
- **Stressors that accelerate multiple attractors** — these are particularly dangerous because they compound the system's drift across multiple failure modes simultaneously
- **Stressors that resist attractors** — (rare but worth finding) — are there stressors that would actually *push the system away* from an unhealthy attractor? (e.g., an OpenAI outage (S-TECH-02) might paradoxically push the team to invest in the AI abstraction layer that would resist Attractor A2)

```
╔═══════════════╗  ╔═══════════════╗  ╔═══════════════╗
║  A1 Monolith  ║  ║  A2 AI Vendor ║  ║  A3 Enterprise║
║  Resurrection ║  ║  Capture      ║  ║  Monoculture  ║
║               ║  ║               ║  ║               ║
║  Stressors:   ║  ║  Stressors:   ║  ║  Stressors:   ║
║               ║  ║               ║  ║               ║
╚═══════════════╝  ╚═══════════════╝  ╚═══════════════╝

╔═══════════════╗  ╔═══════════════╗
║  A4 Services  ║  ║  A5 Privacy   ║
║  Swamp        ║  ║  Collapse     ║
║               ║  ║               ║
║  Stressors:   ║  ║  Stressors:   ║
║               ║  ║               ║
╚═══════════════╝  ╚═══════════════╝
```

---

### Part C — Missing Attractors (10 minutes)

The five attractors in the pre-built set were identified by analysis of the NovaMesh architecture and stressor catalogue. But they may not be complete.

As a team:
1. Look at the stressor-to-attractor map you built in Part B
2. Are there stressors that don't fit neatly into any of the five attractors?
3. Are there patterns of drift in the architecture that none of the five attractors describe?

For each missing attractor you identify:
- Give it a name (descriptive, memorable)
- Describe the stable state (what does the architecture/company look like if it settles here?)
- Identify which stressors accelerate it
- Note which existing attractor it is most similar to (or if it's genuinely novel)

**Suggested prompts for finding missing attractors**:
- "What would happen to NovaMesh if it grew 10x without changing its architecture?"
- "What if NovaMesh ran out of money and had to operate with 40% of its current engineering team?"
- "What if the founders left and the company was acquired by a private equity firm focused on cost extraction?"
- "What if edge AI hardware improved so dramatically that cloud AI became unnecessary?"
- "What if a competitor API emerged that made NovaMesh's platform capability unnecessary (e.g., a universal smart home OS)?"

---

### Part D — Attractor Interaction (5 minutes)

Complex systems can move toward multiple attractors simultaneously. Some attractor pairs are reinforcing; others conflict.

Quick discussion:
- "Which two attractors, if NovaMesh entered both simultaneously, would be most damaging? Why?"
- "Are there any attractors that, if entered, would prevent the system from entering another? What makes them exclusive?"

---

## Output for Exercise 4

Record the following:

1. **Attractor drift scores** (1–5 for each pre-built attractor)
2. **Stressor-to-attractor map** (which stressors accelerate which attractors)
3. **Any missing attractors** discovered by the team, with names and descriptions
4. **The single most dangerous attractor** for NovaMesh right now, and the most important stressor driving it

This becomes direct input to Exercise 4: the residues designed in Exercise 4 should specifically address the identified high-drift attractors.

---

## Reflection Questions

1. Were there any attractors that surprised the team? (Either that NovaMesh was drifting toward one you didn't expect, or *not* drifting toward one you expected)

2. Attractors in Residuality Theory are not always "bad." Could any of the NovaMesh attractors actually represent a *better* outcome than the current architectural trajectory? Under what conditions?

3. The attractors were identified through analysis. Are there attractors that would only be visible if you were *inside* the company as an employee? What would you need to know that isn't in the documentation to identify them?
