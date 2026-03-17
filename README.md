# NovaMesh — Residuality Theory Architecture Demo

This repository contains the architecture documentation and workshop materials for **NovaMesh**, a fictional smart door technology company used as a case study for exploring [Barry O'Reilly's Residuality Theory](https://leanpub.com/residuality).

NovaMesh makes the **NovaDoor** — a smart door device with a built-in 4K camera, electronic lock, and AI-powered facial recognition. The device identifies visitors (enrolled household members, known guests, unknown strangers), automatically unlocks for recognised faces, and sends real-time alerts to the homeowner's mobile app.

## Purpose

This demo is designed for a small group of developers who want to get hands-on experience with Residuality Theory techniques — specifically stressor analysis, attractor mapping, and incidence matrices — applied to a realistic, multi-domain software system.

NovaMesh is a useful case study because it sits at the intersection of **physical hardware** (door locks), **biometric AI** (facial recognition), **real-time cloud infrastructure**, and **sensitive data regulation** (GDPR, BIPA) — creating non-obvious cross-domain stressor patterns.

## What is Residuality Theory?

Residuality Theory (Barry O'Reilly, 2024) is an approach to software architecture grounded in complexity science. Rather than predicting risks and designing around known scenarios, it asks:

> *"What elements of this system will survive when any unexpected stress hits it?"*

A **residue** is a unit of change — the part of the architecture that adapts or persists after a stressor impacts the system. The goal is **criticality**: a state where the existing residues can already handle most new stressors, even those you never anticipated.

Key concepts:
- **Stressors**: Unexpected events that impact the system — technical, regulatory, competitive, organisational, or environmental
- **Residues**: Architectural units designed to absorb or adapt to stress
- **Attractors**: The small set of stable states a complex system gravitates toward under accumulated pressure
- **Incidence Matrix**: A mapping tool that reveals which stressors affect which components, surfacing hidden coupling and vulnerability hotspots
- **Criticality**: The ideal state where existing residues address stressors that weren't explicitly designed for

## Repository Structure

```
docs/
├── company-profile.md              # NovaMesh background, products, strategy
├── architecture/
│   ├── as-is-overview.md           # Current architecture (8 components)
│   ├── component-descriptions.md   # Component catalogue
│   ├── technology-stack.md         # Technologies in use
│   └── diagrams/
│       ├── system-context.md       # C4 Level 1 — System context diagram
│       ├── container-diagram.md    # C4 Level 2 — Containers
│       ├── data-flow.md            # Data flow across the system
│       └── domain-map.md           # Domain model / bounded contexts
├── residuality-theory/
│   ├── overview.md                 # Residuality Theory concepts applied to NovaMesh
│   ├── stressors-catalog.md        # Example stressors (starting point, not exhaustive)
│   ├── attractors-analysis.md      # Three attractor states for NovaMesh
│   └── incidence-matrix.md         # Pre-built stressor × component matrix
└── workshop/
    ├── facilitator-guide.md        # Session guide (~2 hours, ~5 developers)
    └── exercises/
        ├── 01-stressor-identification.md
        ├── 02-incidence-matrix.md
        ├── 03-attractor-analysis.md
        └── 04-residue-redesign.md
```

## Getting Started

1. Read the [Company Profile](docs/company-profile.md) to understand NovaMesh's context and products.
2. Review the [As-Is Architecture Overview](docs/architecture/as-is-overview.md) for a walkthrough of the eight core components.
3. Read the [Residuality Theory Overview](docs/residuality-theory/overview.md) for a primer on the concepts.
4. Use the [Workshop Guide](docs/workshop/facilitator-guide.md) to run a session with your group.

## Suggested Session Flow

| Step | Duration | Focus |
|---|---|---|
| 1 | 15 min | Context sync — walk through NovaMesh architecture together |
| 2 | 20 min | [Exercise 1](docs/workshop/exercises/01-stressor-identification.md) — stressor selection |
| 3 | 35 min | [Exercise 2](docs/workshop/exercises/02-incidence-matrix.md) — build the incidence matrix |
| 4 | 20 min | [Exercise 3](docs/workshop/exercises/03-attractor-analysis.md) — attractor discussion |
| 5 | 20 min | [Exercise 4](docs/workshop/exercises/04-residue-redesign.md) — sketch residues |
| 6 | 10 min | Closing discussion — what's applicable to our actual work? |

Total: approximately **2 hours**

## References

- O'Reilly, B. (2024). *Residues: Time, Change, and Uncertainty in Software Architecture*. LeanPub. https://leanpub.com/residuality
- [Producing a Better Software Architecture with Residuality Theory — InfoQ (2025)](https://www.infoq.com/news/2025/10/architectures-residuality-theory/)
- [Residuality Theory: A Rebellious Take on Building Systems That Actually Survive — Architecture Weekly](https://www.architecture-weekly.com/p/residuality-theory-a-rebellious-take)
- [The Philosophy of Residuality Theory — ScienceDirect](https://www.sciencedirect.com/science/article/pii/S1877050920305561)
- [Book Notes: Residues — Dan Lebrero (2025)](https://danlebrero.com/2025/08/20/residues-time-uncertainty-change-software-architecture-summary/)
