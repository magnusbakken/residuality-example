# Residuality Theory — Applied to NovaMesh

This document introduces Residuality Theory and explains how its concepts apply specifically to the NovaMesh architecture. It is designed to be read before the workshop exercises.

---

## What Is Residuality Theory?

Residuality Theory is a software architecture approach developed by Barry O'Reilly, introduced in his 2024 book *Residues: Time, Change, and Uncertainty in Software Architecture*. It draws from complexity science, philosophy, and software engineering.

The theory makes a foundational observation: **software engineering is the engineering of hyperliminal systems**. Software is ergodic — ordered, deterministic, predictable. But the business context in which software operates is non-ergodic — disordered, complex, constantly surprising. This mismatch is the root cause of why software architectures so frequently fail to remain useful over time.

Traditional architecture approaches respond to this challenge by trying to *predict* the future: identifying risks, writing requirements, applying known patterns. Residuality Theory takes a different position:

> *"The likelihood, order, and scale of impact of stressors cannot be known in advance. Therefore, the goal of architecture is not correctness — it is criticality: systems that can survive unexpected change because their existing residues already handle most new stressors."*

### Core Vocabulary

| Term | Definition |
|---|---|
| **Stressor** | Any unexpected or previously unknown event, change, or pressure that impacts the system — technical, regulatory, competitive, organisational, or environmental |
| **Residue** | A unit of change: an architectural element designed not around a fixed requirement, but around its ability to survive and adapt when stress arrives. The "leftovers" after a stressor hits — what survives and how |
| **Attractor** | A stable state that a complex system gravitates toward, regardless of the number of possible states. Systems don't explore all possible configurations — they cluster around a small number of attractors |
| **Criticality** | The ideal state where it is difficult to imagine a new stressor that isn't already addressed by existing residues |
| **Incidence Matrix** | A table mapping stressors (rows) against residues/components (columns), marking which stressors affect which components. Row and column sums reveal vulnerability hotspots and high-impact stressors |
| **Hyperliminal Coupling** | Hidden coupling between components through shared business specification — components that appear independent but are linked through business rules, regulatory requirements, or shared data semantics |
| **Naive Architecture** | The initial architecture produced without stressor analysis. The starting point for residuality analysis — not a pejorative term |

---

## From Components to Residues

Traditional architecture thinking centres on **components**: bounded, well-defined units with clear responsibilities. Residuality Theory doesn't discard this, but it adds a different lens.

A **residue** asks:

1. *What happens to this part of the system when a specific stressor hits it?*
2. *Does this part survive, degrade gracefully, or fail catastrophically?*
3. *What state does the system settle into after the stressor has passed?*

A component describes structure. A residue describes **behaviour under stress**.

### Example from NovaMesh

Consider the **Notification Service** (C5). As a component, it looks fairly independent — it receives SNS events, sends push/email/SMS, has its own Redis queue. As a residue, a different picture emerges:

- **Stressor: Monolith database unavailable** — The Notification Service cannot read user notification preferences (still stored in the monolith DB). It either sends notifications to all users regardless of opt-out (privacy/compliance risk) or silently fails to send (service degradation).
- **Stressor: Firebase FCM API rate limit exceeded** — Push notifications are dropped with no retry or dead-letter queue.
- **Stressor: SendGrid deliverability issue** — Email delivery silently fails; no fallback channel.

The component appears self-contained. The residue analysis reveals **three hidden failure modes** that were invisible from the component perspective alone.

---

## Attractors in the NovaMesh System

Complex systems don't explore all possible states — they gravitate toward attractors. For software architecture, attractors are the stable configurations that the system naturally settles into when left to evolve under various pressures.

The Residuality Theory workshop will explore **five attractors** for the NovaMesh architecture. Understanding these attractors helps explain *why* the system behaves the way it does under stress, and helps architects design residues that remain viable across multiple attractors.

See the [Attractors Analysis](./attractors-analysis.md) document for the full analysis.

---

## The Residuality Theory Process

When applied to the NovaMesh architecture, the process follows these phases:

### Phase 1: Establish the Naive Architecture

This is already done: the [As-Is Architecture Overview](../architecture/as-is-overview.md) and [Component Catalogue](../architecture/component-descriptions.md) represent the naive architecture. In Residuality Theory, "naive" is not derogatory — it simply means the architecture as designed without explicit stressor simulation.

### Phase 2: Generate Stressors (Random Simulation)

The theory advocates for **random simulation of stressors** rather than systematic risk analysis. The reason: if you only analyse risks you can think of, you recreate the same blind spots that risk analysis has always had. Randomly introducing stressors — even implausible ones — forces the architecture to be examined from angles that systematic thinking would miss.

The [Stressors Catalogue](./stressors-catalog.md) contains a starting set of stressors across six categories:

1. Regulatory & Legal
2. Competitive & Market
3. Technical & Infrastructure
4. Organisational & People
5. Environmental & Physical
6. Reputational & Social

### Phase 3: Build the Incidence Matrix

Map each stressor against each architectural component (or residue). For each intersection, determine: *does this stressor affect this component?* Mark it if yes.

Sum the rows to find **high-impact stressors** (those that affect many components simultaneously). Sum the columns to find **vulnerable components** (those affected by many stressors). Intersections with the highest density reveal hidden coupling — **hyperliminal coupling** between components through shared stress response.

See the [Incidence Matrix](./incidence-matrix.md) for the NovaMesh analysis.

### Phase 4: Identify Attractors

From the incidence matrix, look for patterns: which component clusters tend to fail together? Which stressors tend to trigger cascades? These patterns reveal the attractors — the stable failure (or survival) states the system gravitates toward.

### Phase 5: Design Residues

For each high-impact stressor or vulnerable component identified, design a **residue**: an architectural change, pattern, abstraction, or capability that allows the system to survive or adapt when that stressor arrives. Unlike requirements-driven design, residue design is explicitly about uncertainty — a residue doesn't need to know *when* or *whether* the stressor will arrive; it just needs to make the system more robust when it does.

### Phase 6: Test for Criticality

Apply the new stressors to the updated architecture. Ask: is it now difficult to imagine a stressor that the existing residues couldn't handle? If yes, the architecture approaches criticality.

---

## Why NovaMesh Is a Good Subject for This Analysis

NovaMesh presents a rich set of conditions that make Residuality Theory analysis particularly valuable:

1. **Multi-domain complexity**: Hardware + SaaS + AI creates unusual cross-domain stressor patterns (e.g., a supply chain disruption affects not only physical sales but also the AI training data pipeline and device telemetry coverage)

2. **Mid-migration state**: The architecture is in flux — some components are mature, others are being built, others are planned. This is the most dangerous state for an architecture, because assumptions from the stable components may not hold for the in-flight ones

3. **AI vendor concentration**: The dependency on OpenAI/Anthropic without an abstraction layer is an unusually high-risk coupling that will surface repeatedly in stressor analysis

4. **Regulatory exposure**: GDPR/CCPA in combination with AI-driven personalisation and an IoT data platform creates regulatory stressor vectors that span nearly every domain

5. **Growth-driven pressure**: At 175% YoY growth, the stressors of rapid scale will arrive before the architecture is ready — making the analysis forward-looking as well as current-state

6. **Hidden hyperliminal coupling**: The Notification Service → Monolith DB dependency is a perfect example of the kind of coupling that Residuality Theory is uniquely positioned to surface
