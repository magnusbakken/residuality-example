# Domain Map — Bounded Context Map

This diagram shows the NovaMesh bounded contexts (domains) and their relationships.

```mermaid
graph TD
    subgraph Identity["Identity Domain"]
        ID_CTX["Identity Service (C3)\n🟢 LIVE\nUser auth; household management;\nface profile metadata; RBAC"]
    end

    subgraph Devices["Devices Domain"]
        FLEET_CTX["Device Management (C4)\n🟢 LIVE\nDevice registry; OTA; lock commands"]
        EDGE_CTX["NovaDoor Firmware (C1)\n🟢 LIVE\nEdge AI; lock control; MQTT comms"]
    end

    subgraph AI["AI Domain"]
        FACE_CTX["Facial Recognition (C5)\n🔵 IN-BUILD\nFace matching; AWS Rekognition; recognition results"]
        ACCESS_CTX["Access Rules Engine (C6)\n🔵 IN-BUILD\nRule evaluation; auto-unlock; alert rules"]
    end

    subgraph Customer["Customer Experience Domain"]
        NOTIF_CTX["Notification Service (C7)\n🟢 LIVE\nVisitor alerts; push/email/SMS"]
    end

    subgraph Data["Data Domain"]
        DATA_CTX["Data Store (C8)\n🔵 IN-BUILD\nFace embeddings (pgvector); video (S3); event stream (Kafka)"]
    end

    subgraph Legacy["Legacy Domain"]
        MONO_CTX["Legacy Monolith (C2)\n🟡 MIGRATING\nEnterprise accounts; admin tooling;\n⚠️ notification prefs; legacy subscriptions"]
    end

    ID_CTX -->|"Customer/Supplier\n(identity + household data)"| FLEET_CTX
    ID_CTX -->|"Customer/Supplier\n(face profile metadata)"| FACE_CTX
    ID_CTX -->|"Customer/Supplier\n(entitlement checks)"| ACCESS_CTX

    FLEET_CTX -->|"Published Event\n(visitor_event → face frames)"| FACE_CTX
    FLEET_CTX -->|"Customer/Supplier\n(lock commands → device)"| EDGE_CTX
    FLEET_CTX -->|"Published Event\n(telemetry)"| DATA_CTX

    FACE_CTX -->|"Published Event\n(recognition_result)"| ACCESS_CTX
    FACE_CTX -->|"Published Event\n(recognition_result)"| NOTIF_CTX
    FACE_CTX -->|"Read/write\n(enrolled embeddings)"| DATA_CTX

    ACCESS_CTX -->|"Customer/Supplier\n(issues lock commands)"| FLEET_CTX
    ACCESS_CTX -->|"Published Event\n(rule triggered)"| NOTIF_CTX

    NOTIF_CTX -->|"⚠️ Conformist\n(reads notification prefs from monolith DB)"| MONO_CTX

    MONO_CTX -.->|"To be migrated:\nenterprise accounts + notification prefs"| ID_CTX
```

---

## The Hidden Coupling: Notification Service ↔ Monolith

The `⚠️ Conformist` relationship between the Notification Service and the Legacy Monolith is the most important hidden coupling in this map. The Notification Service reads notification preferences from the monolith database — a dependency that does not appear in any component diagram, but means that a monolith failure disrupts visitor alerts for all customers.

---

## The Biometric Data Domain Problem

Biometric data (face embeddings) has cross-cutting ownership across multiple domains:
- **Identity Service (C3)** owns face profile metadata (which person, which household)
- **Facial Recognition Service (C5)** owns the embeddings themselves and the enrollment process
- **Data Store (C8)** stores face frames, recognition audit logs, and embeddings
- **Access Rules Engine (C6)** uses recognition results to trigger physical door events

There is no single bounded context that owns the full lifecycle of biometric data — from enrollment through consent tracking, use, and deletion. This creates hyperliminal coupling through the **regulatory specification** (GDPR / BIPA): any enforcement action for biometric data touches all four domains simultaneously.

---

## Domain Ownership Summary

| Domain | Component | Status | Priority |
|---|---|---|---|
| Identity | C3 — Identity Service | LIVE | Migration mostly done |
| Devices — Fleet | C4 — Device Management | LIVE | Mostly done |
| Devices — Edge | C1 — NovaDoor Firmware | LIVE | High (model update path) |
| AI — Recognition | C5 — Facial Recognition Service | IN-BUILD | **Critical** |
| AI — Rules | C6 — Access Rules Engine | IN-BUILD | High |
| Customer Experience | C7 — Notification Service | LIVE | Medium (prefs migration) |
| Data | C8 — Data Store | IN-BUILD | **Critical** (biometric governance) |
| Legacy | C2 — Legacy Monolith | MIGRATING | **Eliminate** |
