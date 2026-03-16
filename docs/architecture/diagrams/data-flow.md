# Data Flow Diagrams

Six key data flows across the NovaMesh platform, covering the most architecturally significant paths.

---

## Flow 1: Visitor Detection → Face Recognition → Alert

The primary product flow: a visitor arrives, the NovaDoor detects them, recognition runs, and the homeowner is notified.

```mermaid
sequenceDiagram
    participant Door as NovaDoor Firmware
    participant IoT as AWS IoT Core
    participant DevMgmt as Device Management
    participant Kafka as Kafka (MSK)
    participant FaceRecog as Facial Recognition Engine
    participant FacesDB as Face Embedding Store
    participant Rekognition as AWS Rekognition
    participant AccessRules as Access Rules Engine
    participant Notif as Notification Service
    participant FCM as Firebase FCM
    participant Mobile as Consumer Mobile App

    Door->>Door: Motion detected → person classifier fires
    Door->>IoT: Publish visitor event (face frame, timestamp, device ID)
    IoT->>DevMgmt: Forwards device message
    DevMgmt->>Kafka: Publish visitor_event topic

    Kafka->>FaceRecog: Consume visitor_event

    alt Privacy mode (Insights/Premium — edge recognition)
        Door->>Door: MobileFaceNet runs on-device
        Door->>IoT: Publish recognition_result (identity, confidence)
        IoT->>FaceRecog: Recognition result (no face frame sent to cloud)
    else Cloud mode (Free/Protect)
        FaceRecog->>FacesDB: Load enrolled embeddings for household
        FaceRecog->>Rekognition: Submit face frame for recognition
        Rekognition-->>FaceRecog: Match result (identity ID or "Unknown")
    end

    FaceRecog->>Kafka: Publish recognition_result (identity, confidence, door ID)

    par Access rule evaluation
        Kafka->>AccessRules: Consume recognition_result
        AccessRules->>AccessRules: Evaluate rules for this household + identity
        alt Enrolled face with auto-unlock rule
            AccessRules->>DevMgmt: Issue unlock command
            DevMgmt->>IoT: Publish lock command to device shadow
            IoT->>Door: Unlock command
            Door->>Door: Electronic lock opens
        end
    and Visitor notification
        Kafka->>Notif: Consume recognition_result
        Notif->>Notif: Build alert: name/Unknown, video thumbnail, unlock action
        Notif->>FCM: Send push notification
        FCM->>Mobile: "Alice is at your door" (or "Unknown visitor")
        Mobile->>Mobile: Display alert with video thumbnail + unlock button
    end
```

---

## Flow 2: Face Enrollment

How a household member adds a new recognised face to the system.

```mermaid
sequenceDiagram
    participant User as Consumer (Mobile App)
    participant API as API Gateway
    participant Identity as Identity Service
    participant FaceRecog as Facial Recognition Engine
    participant FacesDB as Face Embedding Store
    participant Subscription as Subscription Service

    User->>API: POST /faces/enroll {name, photos: [frame1, frame2, frame3]}
    API->>API: Validate JWT token
    API->>Subscription: Check entitlement: can this household enroll another face?
    Subscription-->>API: Entitlement result (e.g., Free tier: blocked; Protect: check count ≤ 10)

    alt Over enrollment limit for tier
        API-->>User: 403 Enrollment limit reached — upgrade to Insights
    else Within limit
        API->>FaceRecog: Enroll face {household_id, name, frames}
        FaceRecog->>FaceRecog: Extract embeddings from frames
        FaceRecog->>FaceRecog: Quality check (blur, occlusion, lighting)
        FaceRecog->>FacesDB: Store face embeddings + name + household_id
        FaceRecog->>Identity: Update face profile metadata (face_id, name)
        FaceRecog-->>API: Enrollment result {face_id, quality_score}
        API-->>User: 200 Face enrolled — Alice added to your household
    end
```

---

## Flow 3: Hardware Purchase → Subscription Activation

How buying a NovaDoor hardware unit activates a subscription trial.

```mermaid
sequenceDiagram
    participant Customer as Customer (Browser)
    participant Shopify as Shopify Store
    participant Stripe as Stripe
    participant API as NovaMesh API Gateway
    participant SubSvc as Subscription Service
    participant Monolith as Legacy Monolith
    participant Notif as Notification Service

    Customer->>Shopify: Place order (NovaDoor hardware)
    Shopify->>Stripe: Process payment
    Stripe-->>Shopify: Payment confirmed
    Shopify->>API: POST /webhooks/shopify/order-complete {order_id, customer_email}
    API->>SubSvc: Activate free trial {customer_email, hardware_serial}
    SubSvc->>SubSvc: Create Subscription record (Free tier, 30-day Protect trial)
    SubSvc->>Monolith: ⚠️ Dual-write subscription state (migration state)

    alt Dual-write fails (monolith unreachable)
        Note over SubSvc,Monolith: Subscription created in SubSvc DB<br/>but monolith DB is stale — inconsistency accumulates
    end

    SubSvc->>Notif: Trigger welcome notification
    Notif->>Notif: Read preferences from... monolith DB or cache?
    Note over Notif: ⚠️ Preferences inconsistency — may use wrong channel
    Notif-->>Customer: Welcome email / push notification
```

---

## Flow 4: Remote Door Unlock

How a homeowner unlocks their door remotely from the mobile app.

```mermaid
sequenceDiagram
    participant User as Consumer (Mobile)
    participant API as API Gateway
    participant Identity as Identity Service
    participant Subscription as Subscription Service
    participant DevMgmt as Device Management
    participant IoT as AWS IoT Core
    participant Door as NovaDoor Firmware

    User->>API: POST /doors/{door_id}/unlock
    API->>Identity: Validate JWT; check RBAC (is user authorised for this door?)
    Identity-->>API: Authorised
    API->>Subscription: Check entitlement (remote unlock available on Protect+ only)
    Subscription-->>API: Entitlement granted

    API->>DevMgmt: Issue unlock command {door_id, user_id, timestamp}
    DevMgmt->>IoT: Publish to device shadow: desired lock_state = unlocked
    IoT->>Door: Deliver lock command via MQTT

    alt Device online
        Door->>Door: Actuate electronic lock — door unlocks
        Door->>IoT: Report lock_state = unlocked (acknowledged)
        IoT->>DevMgmt: State sync — confirmed unlock
        DevMgmt-->>API: Unlock confirmed
        API-->>User: Door unlocked ✓
    else Device offline (cloud unreachable from device side)
        Note over IoT,Door: Command queued in device shadow<br/>Lock actuates when device reconnects
        API-->>User: Command sent — will execute when device reconnects
    end
```

---

## Flow 5: OTA Firmware Update

How a firmware update reaches NovaDoor devices in the field. Note the safety implications given the door lock component.

```mermaid
sequenceDiagram
    participant Engineer as NovaMesh Engineer
    participant S3 as S3 (Firmware Artefacts)
    participant DevMgmt as Device Management
    participant IoT as AWS IoT Core
    participant Door as NovaDoor Firmware

    Engineer->>S3: Upload firmware v2.x.x (signed binary)
    Engineer->>DevMgmt: POST /ota/campaigns {version, target_devices, rollout_pct: 5%}
    Note over DevMgmt: Canary rollout: 5% of fleet first

    DevMgmt->>IoT: Publish OTA job to target devices
    IoT->>Door: Notify: new firmware available {download_url, checksum}
    Door->>S3: Download firmware (HTTPS, signed URL)
    Door->>Door: Verify signature + checksum
    Door->>Door: Apply firmware update
    Door->>Door: Reboot...

    alt Update succeeds
        Door->>IoT: Report firmware_version = v2.x.x (online)
        IoT->>DevMgmt: State sync — update confirmed
        DevMgmt->>DevMgmt: Increment success counter; check threshold before expanding rollout
    else Update fails (3–5% of devices)
        Door->>Door: ⚠️ Boot loop or failed verification
        Note over Door: Door lock may be in unknown state
        Door->>IoT: Offline (no heartbeat)
        Note over DevMgmt: ⚠️ No automated rollback<br/>Manual SSH intervention required<br/>Lock state unknown until recovered
        DevMgmt->>DevMgmt: Alert on-call engineer
    end
```

---

## Flow 6: Biometric Data Deletion Request (GDPR / BIPA)

The current (fragmented) process for handling a user's request to delete their biometric and personal data — illustrating the data governance gap.

```mermaid
flowchart TD
    Request["User submits data deletion request\n(GDPR right to erasure / BIPA deletion)"] --> Ticket["Support ticket created in Zendesk"]
    Ticket --> Manual["⚠️ Manual process begins\n(no automated deletion pipeline)"]

    Manual --> Step1["Identity Service:\nDeactivate account + delete user profile"]
    Manual --> Step2["Subscription Service:\nCancel subscription + billing history"]
    Manual --> Step3["Notification Service:\n⚠️ Preferences still in monolith DB\n— requires separate monolith access"]
    Manual --> Step4["Facial Recognition Engine:\nDelete enrolled face embeddings from novamesh-faces-db\n⚠️ No automated deletion API yet"]
    Manual --> Step5["Video Storage:\nDelete all video clips from S3 for this household\n⚠️ Automated if lifecycle policy configured;\nmay miss clips uploaded before policy"]
    Manual --> Step6["Data Platform / Data Lake:\n⚠️ Face frames in Kafka topic backlog\nand S3 archive — no deletion capability\nwithout full re-partitioning"]
    Manual --> Step7["Recognition Audit Log:\n⚠️ Immutable by design — cannot delete\nspecific user's recognition events"]
    Manual --> Step8["AWS Rekognition:\n⚠️ Face data sent to Rekognition for cloud recognition\n— deletion request must be filed separately with AWS"]
    Manual --> Step9["Marketing CRM:\nRequest sent to HubSpot + Customer.io\n⚠️ BIPA consent not tracked in CRM"]

    Step1 & Step2 & Step3 & Step4 & Step5 & Step6 & Step7 & Step8 & Step9 --> Verify["⚠️ Engineer manually verifies each step\n— no automated audit trail of deletion"]

    Verify --> Report["Confirmation sent to user\n(target: 72hrs for GDPR;\nIllinois BIPA requires 30 days)"]

    style Manual fill:#ffdddd,stroke:#cc0000
    style Step3 fill:#ffeecc,stroke:#cc8800
    style Step4 fill:#ffeecc,stroke:#cc8800
    style Step6 fill:#ffeecc,stroke:#cc8800
    style Step7 fill:#ffeecc,stroke:#cc8800
    style Step8 fill:#ffeecc,stroke:#cc8800
    style Step9 fill:#ffeecc,stroke:#cc8800
    style Verify fill:#ffdddd,stroke:#cc0000
```

**Key observation**: The biometric deletion problem is significantly more complex than standard PII deletion. Face embeddings may exist in: the face embedding store (C8 — `novamesh-faces-db`), the Kafka topic backlog (C8), S3 archives (C8), AWS Rekognition's own storage (external), and the recognition audit log (C8). There is currently no single service that can answer: "has all biometric data for this user been deleted?"
