# Domain Map — Bounded Context Map

This diagram shows the NovaMesh bounded contexts (domains) and their relationships, using Domain-Driven Design (DDD) terminology.

```mermaid
graph TD
    subgraph Identity["Identity & Access Domain"]
        ID_CTX["Identity & Access\n(C1 — Identity Service)\n🟢 LIVE"]
    end

    subgraph Commerce["Commerce Domain"]
        SALES_CTX["Sales\n(C7 — Shopify)"]
        BILLING_CTX["Billing\n(C6 — Subscription Service)\n🟡 MIGRATING"]
    end

    subgraph Devices["Devices Domain"]
        FLEET_CTX["Device Fleet\n(C3 — Device Management)\n🟢 LIVE"]
        EDGE_CTX["Edge AI\n(C14 — NovaDoor Firmware)\n🟢 LIVE (partial)"]
    end

    subgraph AI["AI & Intelligence Domain"]
        FACE_CTX["Facial Recognition\n(C9 — Face Recognition Engine)\n🔵 IN-BUILD"]
        VISITOR_CTX["Visitor Intelligence\n(C10 — Visitor Intelligence)\n🔵 IN-BUILD"]
        ACCESS_CTX["Access Rules\n(C11 — Access Rules Engine)\n🔵 IN-BUILD"]
        ORCH_CTX["AI Orchestration\n(C8)\n🔵 IN-BUILD"]
    end

    subgraph Customer["Customer Experience Domain"]
        SUPPORT_CTX["Support\n(C12 — Support Chatbot + Zendesk)"]
        NOTIF_CTX["Notifications\n(C5 — Notification Service)\n🟢 LIVE"]
    end

    subgraph Marketing["Marketing & Growth Domain"]
        MKT_CTX["Marketing & CRM\n(C15 — HubSpot + Customer.io)"]
    end

    subgraph Data["Data & Analytics Domain"]
        DATA_CTX["Data Platform\n(C13 — S3 + Kafka + Snowflake + pgvector)\n🔵 IN-BUILD"]
    end

    subgraph Legacy["Legacy Domain"]
        MONO_CTX["Legacy Monolith\n(C2 — Django)\n🟡 MIGRATING\nEnterprise accounts; notification prefs;\nadmin tooling; legacy subscriptions"]
    end

    ID_CTX -->|"Customer/Supplier\n(Identity owns user + household data;\nother contexts are consumers)"| BILLING_CTX
    ID_CTX -->|"Customer/Supplier"| FLEET_CTX
    ID_CTX -->|"Customer/Supplier"| FACE_CTX
    ID_CTX -->|"Customer/Supplier"| ACCESS_CTX

    SALES_CTX -->|"Published Event\n(order_complete → trial activation)"| BILLING_CTX
    BILLING_CTX -->|"Published Event\n(subscription_changed → entitlement update)"| FACE_CTX
    BILLING_CTX -->|"Published Event\n(subscription_changed → entitlement update)"| ACCESS_CTX
    BILLING_CTX -->|"⚠️ Shared Database\n(dual-write — migration state)"| MONO_CTX

    FLEET_CTX -->|"Published Event\n(visitor_event → face frames)"| FACE_CTX
    FLEET_CTX -->|"Published Event\n(visitor_event → object detection)"| VISITOR_CTX
    FLEET_CTX -->|"Customer/Supplier\n(lock commands → device)"| EDGE_CTX

    FACE_CTX -->|"Published Event\n(recognition_result)"| ACCESS_CTX
    FACE_CTX -->|"Published Event\n(recognition_result)"| NOTIF_CTX
    FACE_CTX -->|"Customer/Supplier\n(routes recognition to vendor)"| ORCH_CTX
    VISITOR_CTX -->|"Context\n(package/visitor context)"| ACCESS_CTX
    VISITOR_CTX -->|"Published Event\n(package detected)"| NOTIF_CTX

    ACCESS_CTX -->|"Customer/Supplier\n(issues lock commands)"| FLEET_CTX
    ACCESS_CTX -->|"Published Event\n(rule triggered)"| NOTIF_CTX

    NOTIF_CTX -->|"⚠️ Conformist\n(reads preferences from monolith DB)"| MONO_CTX

    FACE_CTX -->|"Anti-Corruption Layer\n(translates to/from Rekognition API)"| ORCH_CTX
    SUPPORT_CTX -->|"Conformist (external ticketing system)"| MONO_CTX

    FLEET_CTX -->|"Published Event\n(telemetry)"| DATA_CTX
    FACE_CTX -->|"Published Event\n(recognition events + face frames)"| DATA_CTX
    BILLING_CTX -->|"Published Event\n(billing events)"| DATA_CTX

    MKT_CTX -->|"Anti-Corruption Layer\n(customer events via Airbyte)"| DATA_CTX
```

---

## Domain Ownership Matrix

| Domain | Owning Team | Maturity | Migration Priority |
|---|---|---|---|
| Identity & Access | Platform Engineering | High | Mostly done (~60%) |
| Commerce — Sales | Marketing/Ops | Medium (external) | Low |
| Commerce — Billing | Platform Engineering | Medium (migrating) | High |
| Devices — Fleet | Platform Engineering | High | Mostly done |
| Devices — Edge AI | Firmware + AI/ML | Medium (partial) | High (model update path) |
| AI — Facial Recognition | AI/ML Team | Low (beta) | **Critical** |
| AI — Visitor Intelligence | AI/ML Team | Very Low (PoC) | Medium |
| AI — Access Rules | AI/ML + Platform | Low (beta) | High |
| AI — Orchestration | AI/ML Team | Very Low (40%) | High |
| Customer Experience — Notifications | Platform Engineering | High | Medium (prefs migration) |
| Customer Experience — Support | AI/ML Team | Low (in-build) | Medium |
| Marketing & Growth | Marketing | Medium (external) | Low |
| Data & Analytics | Platform + AI/ML | Low (in-build) | **Critical** (biometric data) |
| Legacy | Platform Engineering | — (diminishing) | **Eliminate** |

---

## The Enterprise Domain Gap

The Enterprise domain — which covers multi-door management, multi-site access control, visitor clearance workflows, and compliance reporting — sits **entirely inside the Legacy Monolith**. There is no extracted bounded context for it. This means:

1. Enterprise features cannot be independently deployed, scaled, or tested
2. All enterprise account data is mixed with consumer data in the `novamesh-monolith-db` database (no tenant isolation at the DB level)
3. Any monolith failure affects enterprise customers disproportionately — their administration capabilities depend entirely on the monolith admin portal

Extracting the Enterprise domain is listed as a migration priority but has not yet started.

---

## The Biometric Data Domain Problem

Unlike standard user PII, biometric data (face embeddings) has cross-cutting ownership:
- **Identity Service** (C1) owns face profile metadata (which person, which household)
- **Facial Recognition Engine** (C9) owns the embeddings themselves and the enrollment process
- **Data Platform** (C13) stores face frames, recognition audit logs, and training data
- **Access Rules Engine** (C11) uses recognition results to trigger physical door events

There is no single bounded context that owns the full lifecycle of biometric data — from enrollment through consent tracking, use, and deletion. This creates a classic hyperliminal coupling through the **regulatory specification** (GDPR / BIPA): any enforcement action for biometric data touches all four domains simultaneously.
