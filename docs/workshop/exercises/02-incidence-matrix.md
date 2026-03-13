# Exercise 2 — Building the Incidence Matrix

**Duration**: 60 minutes  
**Group size**: 4–6 per team  
**Materials**: Blank matrix template, Component Catalogue, team's 10 stressors from Exercise 1

---

## Goal

Build a stressor × component incidence matrix for NovaMesh. Use the matrix to identify:
1. **High-impact stressors** — those that affect many components simultaneously
2. **Vulnerable components** — those affected by many stressors
3. **Hyperliminal coupling** — hidden connections between components revealed by shared stressor response

---

## Background

The incidence matrix is the central analytical tool in Residuality Theory. It replaces the intuitive "what are the risks to this component?" approach with a systematic cross-product analysis.

The key insight is that **hyperliminal coupling** is invisible in most architecture diagrams. Two components may have no direct code or API dependency, yet respond to the same stressor in a way that creates a cascade. The incidence matrix is specifically designed to surface this.

A reference incidence matrix (built from all 32 stressors in the catalogue) is available in [Incidence Matrix](../../residuality-theory/incidence-matrix.md). You should build your own matrix **before** consulting the reference — the process of building it is the value.

---

## The Components

Use the following components (matching the Component Catalogue):

| ID | Component | Status |
|---|---|---|
| C1 | Identity & User Management Service | LIVE |
| C2 | Legacy Monolith | MIGRATING |
| C3 | Device Management Service | LIVE |
| C4 | API Gateway | LIVE |
| C5 | Notification Service | LIVE |
| C6 | Subscription & Billing Service | MIGRATING |
| C7 | E-Commerce (Shopify) | LIVE (EXTERNAL) |
| C8 | AI Orchestration Service | IN-BUILD |
| C9 | Anomaly Detection Engine | IN-BUILD |
| C10 | Predictive Maintenance Engine | IN-BUILD |
| C11 | AI Assistant | IN-BUILD |
| C12 | Support Chatbot | IN-BUILD |
| C13 | Data Platform | IN-BUILD |
| C14 | Edge AI Runtime (Hub Firmware) | LIVE |
| C15 | Marketing & CRM | LIVE (EXTERNAL) |
| C16 | Observability & Operations | LIVE |

---

## Instructions

### Step 1 — Draw the Matrix (5 minutes)

Create a grid on the whiteboard or Miro board:
- **Rows**: Your 10 stressors (from Exercise 1)
- **Columns**: The 16 components (C1–C16)
- Add a **Row Σ** column and a **Column Σ** row for totals

Leave space at the bottom and right for sums.

```
           C1  C2  C3  C4  C5  C6  C7  C8  C9 C10 C11 C12 C13 C14 C15 C16 | Σ
S-REG-01  |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |
S-REG-02  |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |
...       |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |
──────────+────────────────────────────────────────────────────────────────────
Col Σ     |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |    |
```

---

### Step 2 — Fill the Matrix (30 minutes)

For each stressor (row), work across all components:

**For each cell, ask**: *"If this stressor occurs, is this component directly or significantly affected?"*

Use two symbols:
- `●` (filled dot) — **Direct impact**: the stressor directly affects this component's ability to function
- `○` (hollow dot) — **Indirect impact**: the stressor affects this component through a secondary effect, or the component must respond to deal with the stressor

Count `●` as 1 and `○` as 0.5 (or simply count both as 1 for a simpler matrix — the facilitator will advise which approach to use).

**Guidance for each stressor**:

Work through each stressor systematically. For each one:

1. Read the stressor description aloud to the team
2. Identify the **initial impact component** — what is first affected?
3. Trace the **cascade**: what other components are affected because of that initial impact?
4. Mark both direct (●) and indirect (○) impacts
5. Add the row total

**Common prompt when stuck**: "What does this component *need* from the rest of the system, and does this stressor disrupt that dependency?"

**Key probing questions**:
- "Would this component *fail* if this stressor occurred?"
- "Would this component *behave differently* (even if it doesn't fail)?"
- "Would this component be *required to respond* to this stressor?"
- "Does this component hold any data that would be *implicated* by this stressor?"

---

### Step 3 — Calculate Sums (5 minutes)

Sum each row (stressor impact breadth) and each column (component vulnerability score).

Order the stressors by row sum, descending. Order the components by column sum, descending. Write these rankings on the whiteboard.

---

### Step 4 — Pattern Analysis (20 minutes)

This is the most important part of the exercise. Use the following structured analysis:

#### 4a — High-Impact Stressor Analysis (7 minutes)

Look at the stressors with the highest row sums (top 3).

For each:
- "Why does this stressor affect so many components? What is the underlying architectural pattern that causes this?"
- "Is this stressor more dangerous because of *technical* coupling or *business* coupling?"
- "If we could design one architectural change that would reduce this stressor's impact across the most components, what would it be?"

#### 4b — Vulnerable Component Analysis (7 minutes)

Look at the components with the highest column sums (top 3).

For each:
- "Why does this component appear in so many stressor rows? Is it because it's central, or because it's fragile?"
- "Is the high score an indicator of importance (it's core to everything) or fragility (it has no resilience mechanisms)?"
- "What design change would reduce this component's vulnerability score?"

#### 4c — Hyperliminal Coupling Detection (6 minutes)

Scan the matrix for **pairs of columns** that have very similar patterns — that is, columns where `●` marks appear in the same rows.

For each such pair:
- "Do these two components have a direct technical dependency in the architecture diagram?"
- If not: "Why do they share the same stressor response? What is the *hidden coupling* between them?"
- "Is this coupling through shared data? Shared vendor? Shared regulatory requirement? Shared customer trust?"

Name each discovered coupling. Examples:
- "Notification Service and Monolith are coupled through *database co-location*"
- "AI Assistant and Support Chatbot are coupled through *vendor concentration* (both depend on OpenAI)"

---

## Output for Exercise 3 and 4

Record the following from this exercise:

1. **The completed matrix** (photograph or digital capture)
2. **Top 3 high-impact stressors** with a one-sentence explanation of *why* they have high impact
3. **Top 3 vulnerable components** with a one-sentence explanation of *why* they are vulnerable
4. **Named hyperliminal couplings** — at least 2 pairs with a description of the hidden coupling mechanism

These become the input to Exercise 3 (Attractor Analysis) and Exercise 4 (Residue Redesign).

---

## Reflection Questions

Before moving on, spend 2 minutes discussing:

1. Were there any stressors that affected fewer components than expected? What does that tell you about that part of the architecture?
2. Were there any components that had lower vulnerability scores than expected? Why might that be?
3. Did building the matrix change your intuitive view of the architecture in any way?
