# NovaMesh — Residuality Theory Architecture Demo

This repository contains the architecture documentation and workshop materials for **NovaMesh**, a fictional smart door technology company used as a case study for exploring [Barry O'Reilly's Residuality Theory](https://leanpub.com/residuality).

NovaMesh makes the **NovaDoor** — a smart door device with a built-in 4K camera, electronic lock, and AI-powered facial recognition. The device identifies visitors (enrolled household members, known guests, unknown strangers), automatically unlocks for recognised faces, and sends real-time alerts to the homeowner's mobile app. Optional enterprise features support multi-door building access control, visitor management, and compliance reporting.

## Purpose

This demo is designed for architects, developers, and stakeholders who want to apply Residuality Theory techniques — specifically **stressor analysis**, **attractor mapping**, and **incidence matrices** — to a realistic, multi-domain software system. The NovaMesh architecture intentionally represents a company mid-transition: with legacy components, ongoing migrations, newly built microservices, and ambitious AI capabilities either in-flight or planned.

NovaMesh is a particularly rich subject because it sits at the intersection of **physical hardware** (door locks), **biometric AI** (facial recognition), **real-time cloud infrastructure**, and **sensitive data regulation** (GDPR, BIPA) — creating unusual and non-obvious cross-domain stressor patterns.

## What is Residuality Theory?

Residuality Theory (Barry O'Reilly, 2024) is an approach to software architecture grounded in complexity science. Rather than predicting risks and designing around known scenarios, it asks:

> *"What elements of this system will survive when any unexpected stress hits it?"*

A **residue** is a unit of change — the part of the architecture that adapts or persists after a stressor impacts the system. The goal is **criticality**: a state where the existing residues can already handle most new stressors, even those you never anticipated.

Key concepts:
- **Stressors**: Unexpected or previously unknown events that impact the system (technical, regulatory, competitive, organisational, or environmental)
- **Residues**: Architectural units designed to absorb or adapt to stress — alternatives to traditional "components"
- **Attractors**: The small set of stable states a complex system gravitates toward, regardless of how many possible states exist
- **Incidence Matrix**: A mapping tool that shows which stressors affect which residues/components, revealing hidden coupling and vulnerability hotspots
- **Criticality**: The ideal architectural state where existing residues address stressors that were not explicitly designed for

## Repository Structure

```
docs/
├── company-profile.md              # NovaMesh background, products, strategy
├── architecture/
│   ├── as-is-overview.md           # Current architecture narrative
│   ├── component-descriptions.md   # Detailed component catalogue
│   ├── technology-stack.md         # Technologies in use
│   └── diagrams/
│       ├── system-context.md       # C4 Level 1 — System context diagram
│       ├── container-diagram.md    # C4 Level 2 — Containers
│       ├── data-flow.md            # Data flow across the system
│       └── domain-map.md           # Domain model / bounded contexts
├── residuality-theory/
│   ├── overview.md                 # Residuality Theory concepts applied to NovaMesh
│   ├── stressors-catalog.md        # Catalogue of external and internal stressors
│   ├── attractors-analysis.md      # Attractor states for NovaMesh
│   └── incidence-matrix.md         # Stressor × Residue incidence matrix
└── workshop/
    ├── facilitator-guide.md        # How to run the Residuality Theory workshop
    └── exercises/
        ├── 01-stressor-identification.md
        ├── 02-incidence-matrix.md
        ├── 03-attractor-analysis.md
        └── 04-residue-redesign.md
```

## Getting Started

1. Read the [Company Profile](docs/company-profile.md) to understand NovaMesh's context, products, and strategic situation.
2. Review the [As-Is Architecture Overview](docs/architecture/as-is-overview.md) to understand the current system.
3. Explore the [Architecture Diagrams](docs/architecture/diagrams/) for visual representations.
4. Read the [Residuality Theory Overview](docs/residuality-theory/overview.md) for an introduction to the concepts as applied here.
5. Use the [Workshop Facilitator Guide](docs/workshop/facilitator-guide.md) to run a structured session with your team.

## Recommended Workshop Flow

| Session | Duration | Focus |
|---|---|---|
| 1 | 30 min | Company context & architecture walkthrough |
| 2 | 45 min | Exercise 1 — Stressor identification |
| 3 | 60 min | Exercise 2 — Building the incidence matrix |
| 4 | 45 min | Exercise 3 — Attractor analysis |
| 5 | 60 min | Exercise 4 — Residue redesign proposals |
| 6 | 30 min | Debrief & synthesis |

Total: approximately **4.5–5 hours** (full-day workshop with breaks)

## References

- O'Reilly, B. (2024). *Residues: Time, Change, and Uncertainty in Software Architecture*. LeanPub. https://leanpub.com/residuality
- [Producing a Better Software Architecture with Residuality Theory — InfoQ (2025)](https://www.infoq.com/news/2025/10/architectures-residuality-theory/)
- [Residuality Theory: A Rebellious Take on Building Systems That Actually Survive — Architecture Weekly](https://www.architecture-weekly.com/p/residuality-theory-a-rebellious-take)
- [The Philosophy of Residuality Theory — ScienceDirect](https://www.sciencedirect.com/science/article/pii/S1877050920305561)
- [Book Notes: Residues — Dan Lebrero (2025)](https://danlebrero.com/2025/08/20/residues-time-uncertainty-change-software-architecture-summary/)
