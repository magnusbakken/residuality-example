# Workshop Guide — Residuality Theory with NovaMesh

This guide describes a lightweight session for a small group of developers who want to explore Residuality Theory techniques and see whether they're worth applying in everyday work.

---

## Session Overview

**Purpose**: Get hands-on experience with Residuality Theory techniques — stressor selection, incidence matrices, attractor identification, and residue design — using NovaMesh as a realistic case study. The goal is exploration, not a formal deliverable.

**Group size**: ~5 people (all developers — no need for mixed roles)

**Duration**: ~2 hours

**Format**: One group, working together. No team splits, no formal facilitation role, no strict agenda. Move at whatever pace feels right.

---

## Before the Session

Everyone should spend 20–30 minutes reading:
1. [Company Profile](../company-profile.md) — who NovaMesh is and what NovaDoor does
2. [As-Is Architecture Overview](../architecture/as-is-overview.md) — the eight core components

That's it. The rest can be discovered during the session.

Optionally, skim the [Residuality Theory Overview](../residuality-theory/overview.md) if you want a conceptual primer beforehand.

---

## Session Flow

### 1. Context sync (15 min)

A quick conversation to make sure everyone shares the same mental model of NovaMesh.

Walk through the eight components together. The key things to highlight:
- NovaMesh controls a physical door lock — architectural failures have safety consequences
- The architecture is mid-migration: some things are live, some half-built, some barely started
- The facial recognition chain (C5 → C6 → C4 → C1) is the core product feature and the most interesting thing to stress-test
- There's a hidden coupling: the Notification Service reads notification preferences from the monolith database

A good opening question: *"What's the most uncomfortable assumption baked into this architecture?"*

---

### 2. Stressor selection (20 min)

Read through the [Stressors Catalogue](../residuality-theory/stressors-catalog.md) together. Pick 5–7 stressors that the group finds most interesting — not necessarily the most "realistic." Include at least one from each of these categories: technical, regulatory, organisational, environmental.

Then: generate at least one stressor that the catalogue doesn't contain. What's missing? What would *you* be worried about if you were an engineer at NovaMesh?

If time allows, generate one deliberately implausible stressor — something that feels unlikely but is technically conceivable. These often surface the most interesting architectural assumptions.

*No dot-voting or formal prioritisation needed. Just talk and agree on a set.*

---

### 3. Build the incidence matrix (35 min)

This is the core exercise. Use the 8 components from the [Component Catalogue](../architecture/component-descriptions.md) as columns and your chosen stressors as rows.

For each stressor, work across the components and ask: *"If this stressor occurs, is this component affected?"* Mark direct impacts (`●`) and indirect/secondary impacts (`○`).

When you're done, sum the rows and columns. Look for:
- **Highest row sums**: which stressors affect the most components?
- **Highest column sums**: which components are most exposed?
- **Column pairs with similar patterns**: are there components that always get marked together, even though they look independent? This is hyperliminal coupling.

The [pre-built matrix](../residuality-theory/incidence-matrix.md) is available for comparison after you build your own — but build yours first.

*Don't rush this. The most interesting insights come from disagreements: "Wait, would this stressor affect C7? Why?"*

---

### 4. Attractor discussion (20 min)

Read the three attractors in the [Attractors Analysis](../residuality-theory/attractors-analysis.md).

Discuss:
- Which attractor does the matrix suggest NovaMesh is drifting toward most strongly?
- Which stressors from your set push toward multiple attractors simultaneously?
- Are there attractors that aren't in the pre-built set? What drift patterns does *your* matrix reveal?

Attractors don't have to be bad. Is there a scenario where settling into one of these attractors might actually be the right outcome for NovaMesh?

---

### 5. Residue sketching (20 min)

From the matrix and attractor discussion, pick one or two architectural changes that would make NovaMesh more resilient. These are residues — not point fixes for a single stressor, but architectural capabilities that improve the system's response to a whole category of stress.

For each residue, briefly sketch:
- What is this capability? (describe it, don't specify an implementation)
- Which stressors does it address, and how?
- Which attractor does it resist?
- What does the system *do* when a stressor arrives, if this residue is in place?
- What does adopting it cost or complicate?

You don't need to fill in a formal template. A few sentences and a quick diagram on the whiteboard is enough.

---

### 6. Closing discussion (10 min)

Step back and discuss:
- Was there a moment in the session where an assumption about the architecture was revealed that surprised you?
- Is there anything here that's applicable to the systems you actually work on? What would the stressors and components look like for your own architecture?
- Would you run this kind of session on your own codebase? What would you need to prepare?

---

## What Makes a Good Session

**The most valuable output is rarely the residue designs.** It's the *disagreements that surface during the incidence matrix* — the moments when someone says "wait, would *that* component actually be affected by this?" and the team discovers that their mental models of the architecture are different.

**Don't get stuck on probability.** If someone says "that stressor would never happen," ask: "What architectural assumption makes you confident of that? Is that assumption safe?" The point isn't to predict the future — it's to examine what the architecture does when stress arrives.

**The implausible stressor often produces the best discussion.** A stressor that feels ridiculous frequently exposes a real coupling or assumption that more "realistic" stressors would have missed.

**It's fine to go off-script.** If the group finds a particularly interesting thread during the matrix-building phase and wants to stay there longer, do that. The structure exists to create productive discussion, not constrain it.

---

## References

- O'Reilly, B. (2024). *Residues: Time, Change, and Uncertainty in Software Architecture*. LeanPub. https://leanpub.com/residuality
- [Producing a Better Software Architecture with Residuality Theory — InfoQ (2025)](https://www.infoq.com/news/2025/10/architectures-residuality-theory/)
- [Residuality Theory: A Rebellious Take on Building Systems That Actually Survive — Architecture Weekly](https://www.architecture-weekly.com/p/residuality-theory-a-rebellious-take)
- [NDC Residuality Theory Workshop — Barry O'Reilly](https://ndcworkshops.com/slot/residuality-theory-workshop-oneday/)
