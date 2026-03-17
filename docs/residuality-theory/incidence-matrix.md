# Stressor × Component Incidence Matrix

The incidence matrix is the central analytical tool in Residuality Theory. It maps **stressors** (rows) against **architectural components** (columns), marking which stressors affect which components.

By summing rows and columns, we can identify:
- **High-impact stressors** (high row sum): those that affect many components simultaneously
- **Vulnerable components** (high column sum): those affected by many stressors

---

## Component Key

| ID | Component |
|---|---|
| C1 | NovaDoor Firmware |
| C2 | Legacy Monolith |
| C3 | Identity Service |
| C4 | Device Management Service |
| C5 | Facial Recognition Service |
| C6 | Access Rules Engine |
| C7 | Notification Service |
| C8 | Data Store |

---

## The Matrix

A `●` indicates that the stressor directly or significantly affects the component. A `○` indicates indirect or secondary impact.

| Stressor | C1 | C2 | C3 | C4 | C5 | C6 | C7 | C8 | **Row Σ** |
|---|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **S-TECH-01** Rekognition API Breaking Change | | | | | ● | ○ | ○ | | **3** |
| **S-REG-02** GDPR Enforcement (Biometric) | | ● | ● | | ● | ● | ○ | ● | **6** |
| **S-ENV-01** Cloud Region Outage | ○ | ● | ● | ● | ● | ● | ● | ● | **8** |
| **S-ORG-01** Key AI Engineer Resignation | ○ | | | | ● | ● | | ○ | **4** |
| **S-REP-02** False Recognition Causes Harm | ○ | | | ○ | ● | ● | ● | | **5** |
| **Column Σ** | **3** | **2** | **2** | **2** | **5** | **5** | **4** | **2** | |

---

## Analysis

### High-Impact Stressors (Row Sums)

| Stressor | Row Σ | Key Observation |
|---|---|---|
| **S-ENV-01** Cloud Region Outage | **8** | NovaMesh has no multi-region strategy. A single region failure affects virtually every service — including face recognition and lock commands. NovaDoor devices in edge mode continue to auto-unlock locally, but with no cloud notification. |
| **S-REG-02** GDPR Enforcement (Biometric) | **6** | Biometric GDPR enforcement is uniquely severe: it simultaneously touches identity, AI recognition, billing, data storage, and admin tooling. No single team can respond alone. |
| **S-REP-02** False Recognition Causes Harm | **5** | Physical safety consequence. The false accept rate problem is structural, not a one-off — 2% FAR × 115K devices means this is a recurring risk, not a one-time incident. |

### Vulnerable Components (Column Sums)

| Component | Column Σ | Key Observation |
|---|---|---|
| **C5** Facial Recognition Service | **5** | The core product differentiator is also the most architecturally exposed component — affected by technical, regulatory, organisational, and reputational stressors simultaneously. |
| **C6** Access Rules Engine | **5** | Sits at the intersection of recognition results, lock commands, and billing entitlements. A failure anywhere upstream (C5) or in dependencies (subscription state) can silently disable auto-unlock. |
| **C7** Notification Service | **4** | The primary consumer experience ("know who's at your door") depends on this service — but it has a hidden coupling to the monolith through notification preferences. |

---

## Hyperliminal Coupling

The incidence matrix reveals **hyperliminal coupling** — components that appear independent but are linked through shared stressor response.

### Coupling 1: Notification Service ↔ Legacy Monolith

- **S-ENV-01** (Cloud Region Outage) affects both C2 and C7
- **S-REG-02** (GDPR Enforcement) requires coordinated response from both C2 and C7

The Notification Service is nominally independent, but reads notification preferences from the monolith database. A monolith failure means alerts may fire to the wrong channels, or fail to send at all — affecting the core consumer experience.

### Coupling 2: Facial Recognition ↔ Access Rules Engine ↔ Device Management (The Auto-Unlock Chain)

C5 → C6 → C4 → C1 is the critical path for auto-unlock. All four components must be working. But they are affected by *different* stressors:
- C5 is affected by AI stressors (S-TECH-01, S-ORG-01) and accuracy issues (S-REP-02)
- C6 is affected by entitlement stressors and rule logic bugs
- C4 is affected by infrastructure stressors (S-ENV-01)
- C1 is only affected indirectly — it can continue to operate locally

A resident relying on auto-unlock to enter their home could find it fails for any of these independently occurring reasons, with no indication which part of the chain has failed.

### Coupling 3: Biometric Data Cross-Domain Exposure

**S-REG-02** affects six components simultaneously. This reveals that any regulatory enforcement involving biometric data creates a crisis that touches identity (C3), AI (C5, C6), notifications (C7), the monolith's admin tooling (C2), and the data store (C8). No single team owns the biometric data response — it requires simultaneous coordination across every domain.

---

## This Matrix Is a Starting Point

This pre-built matrix covers the five example stressors from the catalogue. In the workshop, you'll build your own matrix with the stressors your group chooses — which will likely reveal different patterns and different couplings depending on what you consider important.

The value is not in consulting the answer — it's in the process of building it.
