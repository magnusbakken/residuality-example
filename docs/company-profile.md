# NovaMesh — Company Profile

## Overview

**NovaMesh** is a smart door technology company that designs and sells the **NovaDoor** — an AI-powered smart door device with a built-in camera, electronic lock, and facial recognition capabilities. Founded in 2022 in Austin, Texas, NovaMesh has grown rapidly from a hardware startup to a software-led business built on the conviction that knowing who is at your door — and acting on that knowledge automatically — is a fundamental home and building security need.

> **Tagline**: *"Your door, intelligently aware."*

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

### 1. NovaDoor (Hardware)

The NovaDoor is a smart door unit that replaces or augments a standard residential or commercial door. It includes:

- **4K HDR wide-angle camera** with IR night vision and HDR for challenging lighting conditions
- **Electronic deadbolt lock** with manual key override
- **Embedded edge AI processor** (dedicated NPU) running local inference models
- **Two-way audio** — built-in microphone and speaker for remote intercom
- **Motion and person detection** — distinguishes between people, animals, and background movement
- **Tamper detection** — alerts when the device is physically interfered with

The NovaDoor's core value proposition is **AI-powered facial recognition**: the device can identify enrolled household members and known visitors, automatically unlock for recognised faces, and send real-time alerts with a labelled video clip when an unknown person approaches. Rules can be configured to trigger different automated responses depending on who is detected.

- Sold direct-to-consumer via the NovaMesh website and Amazon
- Also available through selected smart home retailer partnerships (Best Buy, Home Depot)
- Hardware margin is intentionally thin; the business model depends on subscription attachment

**Current installed base**: ~115,000 active NovaDoor units across ~95,000 households and ~400 enterprise locations

### 2. NovaDoor Home (Consumer Subscription)

A tiered subscription service that unlocks AI capabilities on the NovaDoor device.

| Tier | Price | Features |
|---|---|---|
| **Free** | $0/mo | Live camera view, motion alerts, 7-day video clip history, visitor log |
| **Protect** | $8.99/mo | Facial recognition (up to 10 enrolled faces), auto-unlock for recognised faces, 30-day history, visitor notifications with face labels |
| **Insights** | $14.99/mo | Unlimited enrolled faces, AI visitor intelligence, package detection, advanced access rules, 90-day history, privacy mode (edge-only recognition) |
| **Premium** | $24.99/mo | All features + 1-year history, priority support, end-to-end encrypted video, multi-door household management |

~68% of hardware customers activate a paid tier within 90 days.

### 3. NovaDoor Enterprise

A B2B offering targeting property management companies, hospitality, corporate campuses, and multi-unit residential buildings. Includes multi-door and multi-site management, visitor management workflows, access audit logs, compliance reporting, and RBAC for facility administrators.

- ~400 enterprise accounts, average contract value $18,000/year
- Enterprise is growing faster than consumer; executive team is considering a strategic pivot toward B2B
- Key use cases: office building access control, hotel room access, apartment building front doors, visitor check-in at reception

---

## Technical Strategy

NovaMesh is in the middle of a significant architectural transition. The original product was built quickly to validate market fit, resulting in a **Django monolith** that handles user accounts, subscriptions, billing, and door device communication. As the company scaled, this became a bottleneck.

The current strategy is to **decompose the monolith** into domain-oriented microservices while simultaneously building the **AI Platform** — specifically the facial recognition, visitor intelligence, and access rules capabilities that are central to the company's product differentiation.

Key strategic bets:
1. **AI-first product** — every subscription tier must have a demonstrable AI feature; facial recognition is the core moat
2. **Enterprise pivot** — double enterprise ARR within 18 months
3. **Privacy-by-design** — biometric data (face embeddings) must be processable entirely on-device (edge-only mode) to address regulatory and consumer privacy concerns
4. **Edge + cloud** — facial recognition runs on-device for speed and privacy; cloud handles model training, aggregated visitor intelligence, and fallback recognition

---

## Organisational Structure

```
CEO
├── CTO
│   ├── Platform Engineering (12 engineers) — microservices, infrastructure
│   ├── AI/ML Team (8 engineers/scientists) — facial recognition, visitor intelligence, edge models
│   ├── Mobile & Frontend (7 engineers) — iOS, Android, web app
│   ├── Firmware Team (5 engineers) — NovaDoor firmware, edge AI runtime
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
| Ring (Amazon) | High | Deep Amazon ecosystem integration; video doorbell market leader; announced face recognition feature |
| Google Nest Doorbell | High | Google AI; Familiar Faces feature already deployed; ecosystem lock-in |
| Eufy Security | Medium | Strong privacy positioning (local storage); growing face recognition capability |
| Arlo | Medium | Premium camera quality; limited AI; no face recognition in consumer tier |
| Verkada (Enterprise) | Medium-High | Enterprise building security; advanced face recognition; higher price point |
| Latch (Enterprise) | Low-Medium | Smart access control for multifamily; less AI-focused |

**Key differentiator NovaMesh must protect**: the combination of on-device (privacy-preserving) face recognition + cloud AI visitor intelligence + open access rules API + enterprise-grade compliance reporting.

---

## Current Challenges

1. **Monolith decomposition** is ongoing, with some services already extracted and others still tightly coupled in the legacy codebase.
2. **AI Platform** is 6 months into development; the facial recognition engine and access rules capabilities are in beta, but the infrastructure for serving them at scale is still being built.
3. **Device fleet management** at 115K+ NovaDoor units is straining the current IoT platform; over-the-air (OTA) firmware update failures affect ~3–5% of updates — with door lock safety implications.
4. **Enterprise multi-tenancy** is partially implemented; some larger enterprise customers are running workarounds to achieve tenant and site isolation.
5. **Biometric data regulation**: Facial recognition biometric data is subject to increasingly strict regulations (Illinois BIPA, EU AI Act, various US city-level bans); the company's data governance approach is inconsistent across services.
6. **Data governance**: GDPR, CCPA, and BIPA compliance for biometric face embeddings is handled inconsistently across services; a unified consent and biometric data management layer is planned but not yet implemented.
7. **On-call fatigue**: With rapid hiring, engineering ownership of production services is unclear for several components.
