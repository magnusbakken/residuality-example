# Exercise 2 — Building the Incidence Matrix

**~35 minutes**

---

## Goal

Build a stressor × component matrix to identify which stressors have the broadest architectural impact, which components are most exposed, and where hidden coupling exists between components that appear independent.

---

## The Components

Use these 8 components as your columns (from the [Component Catalogue](../../architecture/component-descriptions.md)):

| ID | Component | Status |
|---|---|---|
| C1 | NovaDoor Firmware | LIVE |
| C2 | Legacy Monolith | MIGRATING |
| C3 | Identity Service | LIVE |
| C4 | Device Management Service | LIVE |
| C5 | Facial Recognition Service | IN-BUILD |
| C6 | Access Rules Engine | IN-BUILD |
| C7 | Notification Service | LIVE |
| C8 | Data Store | IN-BUILD |

---

## How to Build It

Draw a grid: your stressors from Exercise 1 as rows, the 8 components as columns. Add a row and column for totals.

For each stressor, work across the components and ask:

> *"If this stressor occurs, is this component affected?"*

Mark:
- `●` — direct impact: the stressor directly affects this component's ability to function
- `○` — indirect impact: the stressor affects this component through a secondary effect, or the component must respond to deal with it

Work through each stressor systematically:
1. Read the stressor description aloud
2. Identify the first component affected
3. Trace the cascade: what other components are affected because of that initial impact?
4. Mark and move on

When you're stuck, ask: *"What does this component need from the rest of the system, and does this stressor disrupt that?"*

Sum each row (how broadly does this stressor affect the system?) and each column (how many stressors affect this component?).

---

## What to Look For

**High row sums**: Why does this stressor affect so many components? Is it because of technical coupling, shared data, or regulatory exposure that crosses every domain?

**High column sums**: Why does this component appear in so many stressor rows? Is it because it's architecturally central, or because it's fragile and has no resilience mechanisms?

**Similar column patterns**: Scan for pairs of columns where `●` marks appear in the same rows. These components are responding to the same stressors — even if they have no direct dependency in the architecture diagram. This is hyperliminal coupling.

When you find a hyperliminal coupling, name it:
- *"C7 and C2 are coupled through the monolith notification preferences database"*
- *"C5 and C6 are coupled through the recognition result dependency — a recognition outage disables auto-unlock even though they're separate services"*

---

## A Few Questions to Discuss Along the Way

- Were there any stressors that affected fewer components than expected? What does that tell you about that part of the architecture?
- Were there any components that had lower vulnerability scores than you expected?
- Did building the matrix change your intuitive view of the architecture?

---

## Output

Before moving to Exercise 3:
1. The completed matrix
2. The top 3 highest-impact stressors (row sums), with one sentence on *why* each is high
3. The top 3 most vulnerable components (column sums), with one sentence on *why* each is high
4. At least one named hyperliminal coupling you discovered
