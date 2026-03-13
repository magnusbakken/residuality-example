# NovaMesh — Company Profile

## Overview

**NovaMesh** is a smart home technology company that sells AI-enabled mesh networking hardware and a companion SaaS subscription platform. Founded in 2022 in Austin, Texas, NovaMesh has grown rapidly from a hardware startup to a software-led business with an expanding enterprise offering.

> **Tagline**: *"Intelligence woven into your world."*

| Attribute | Detail |
|---|---|
| Founded | 2022 |
| Headquarters | Austin, TX (US) |
| Employees | ~160 |
| Customers | ~95,000 households, ~400 enterprise accounts |
| ARR | $14.2M (growing ~175% YoY) |
| Funding | Series A ($22M, 2023); Series B ($55M, Q4 2024) |
| Business Model | Hardware + SaaS subscription + B2B enterprise |

---

## Products

### 1. NovaMesh Hub (Hardware)

A next-generation mesh Wi-Fi node with an embedded edge AI processor. Each hub runs local inference models for home automation, anomaly detection, and privacy-preserving data processing.

- Sold direct-to-consumer via the NovaMesh website and Amazon
- Also available through selected ISP partnerships
- Hardware margin is intentionally thin; the business model depends on subscription attachment

**Current installed base**: ~115,000 active devices across ~95,000 households

### 2. NovaMesh Home (Consumer Subscription)

A tiered subscription service that unlocks AI capabilities on the NovaMesh Hub.

| Tier | Price | Features |
|---|---|---|
| **Free** | $0/mo | Basic connectivity management, device dashboard |
| **Protect** | $8.99/mo | AI threat detection, parental controls, VPN |
| **Insights** | $14.99/mo | Energy optimisation, predictive device maintenance, AI assistant |
| **Premium** | $24.99/mo | All features + priority support, unlimited history |

~68% of hardware customers activate a paid tier within 90 days.

### 3. NovaMesh Enterprise

A B2B offering targeting property management companies, hospitality, and corporate campuses. Includes multi-site management, tenant isolation, compliance reporting, and fleet device management.

- ~400 enterprise accounts, average contract value $18,000/year
- Enterprise is growing faster than consumer; executive team is considering a strategic pivot toward B2B

---

## Technical Strategy

NovaMesh is in the middle of a significant architectural transition. The original product was built quickly to validate market fit, resulting in a **Django monolith** that handles user accounts, subscriptions, billing, and device communication. As the company scaled, this became a bottleneck.

The current strategy is to **decompose the monolith** into domain-oriented microservices while simultaneously building the **AI Platform** — a new capability that is central to the company's product differentiation and competitive moat.

Key strategic bets:
1. **AI-first product** — every subscription tier must have a demonstrable AI feature
2. **Enterprise pivot** — double enterprise ARR within 18 months
3. **Platform play** — open NovaMesh AI capabilities to third-party home automation apps via API
4. **Edge + cloud** — sensitive data processed on-device; aggregated insights computed in cloud

---

## Organisational Structure

```
CEO
├── CTO
│   ├── Platform Engineering (12 engineers) — microservices, infrastructure
│   ├── AI/ML Team (8 engineers/scientists) — AI Platform, edge models
│   ├── Mobile & Frontend (7 engineers) — iOS, Android, web app
│   ├── Firmware Team (5 engineers) — Hub firmware, edge runtime
│   └── DevOps/SRE (4 engineers)
├── CPO (Product)
│   ├── Consumer Product (3 PMs)
│   └── Enterprise Product (2 PMs)
├── Chief Revenue Officer
│   ├── Sales (10 AEs, focused on enterprise)
│   ├── Marketing (8 people, heavy on digital/AI-driven campaigns)
│   └── Customer Success (6 CSMs, enterprise-focused)
└── CFO / Operations
```

---

## Competitive Landscape

| Competitor | Threat Level | Notes |
|---|---|---|
| Google Nest | High | Deep integration with Google AI; enormous ecosystem |
| Amazon Eero | High | Native Alexa integration; aggressive pricing |
| Cisco Meraki (SMB) | Medium | Enterprise networking, less AI-focused |
| Eero Pro | Medium | Strong brand; limited AI |
| Firewalla | Low-Medium | Privacy-focused niche; growing community |
| Plume (ISP white-label) | Medium | ISP partnerships threaten NovaMesh's ISP channel |

**Key differentiator NovaMesh must protect**: the combination of edge AI processing (privacy-preserving) + cloud AI insights + open third-party platform.

---

## Current Challenges

1. **Monolith decomposition** is ongoing, with some services already extracted and others still tightly coupled in the legacy codebase.
2. **AI Platform** is 6 months into development; the recommendation and anomaly detection models are in beta, but the infrastructure for serving them at scale is still being built.
3. **Device fleet management** at 115K+ devices is straining the current IoT platform; over-the-air (OTA) firmware update failures affect ~3–5% of updates.
4. **Enterprise multi-tenancy** is partially implemented; some larger enterprise customers are running workarounds to achieve tenant isolation.
5. **Vendor concentration**: Several critical AI capabilities are built on third-party LLM APIs (OpenAI, Anthropic); the team has not yet built meaningful abstraction layers.
6. **Data governance**: GDPR and CCPA compliance is handled inconsistently across services; a unified consent and data management layer is planned but not yet implemented.
7. **On-call fatigue**: With rapid hiring, engineering ownership of production services is unclear for several components.
