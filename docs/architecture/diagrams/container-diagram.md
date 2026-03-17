# Container Diagram (C4 Level 2)

This diagram shows the eight core components of the NovaMesh platform.

**Legend**:
- 🟢 LIVE — in production
- 🟡 MIGRATING — being rebuilt or extracted
- 🔵 IN-BUILD — active development, not yet in production

```mermaid
C4Container
    title NovaMesh — Container Diagram

    Person(consumer, "Consumer")
    Person(enterprise_admin, "Enterprise Admin")

    System_Boundary(edge, "Edge Layer") {
        Container(novadoor_fw, "NovaDoor Firmware 🟢", "C / Embedded Linux / TFLite", "Person detection; on-device face recognition (privacy mode); lock control; MQTT comms to cloud")
    }

    System_Boundary(core, "Core Services Layer") {
        Container(monolith, "Legacy Monolith 🟡", "Python/Django / PostgreSQL 2.1TB", "Enterprise accounts; admin tooling; legacy subscriptions; ⚠️ notification preferences")
        Container(identity, "Identity Service 🟢", "Go 1.22 / PostgreSQL", "User auth; household management; face profile metadata; RBAC")
        Container(device_mgmt, "Device Management 🟢", "Go 1.22 / DynamoDB / IoT Core", "Device registry; lock command dispatch; OTA updates; door state shadow")
        Container(notification, "Notification Service 🟢", "Node.js / Redis / FCM / SendGrid", "Visitor alerts with face label; push/email/SMS delivery")
    }

    System_Boundary(ai, "AI Layer") {
        Container(face_recog, "Facial Recognition Service 🔵", "Python 3.12 / MobileFaceNet / AWS Rekognition", "Matches visitor face frames against enrolled embeddings; publishes recognition results")
        Container(access_rules, "Access Rules Engine 🔵", "Python 3.12 / PostgreSQL", "Real-time rule evaluation; auto-unlock; alert rules; lock command dispatch")
    }

    System_Boundary(data, "Data Layer") {
        ContainerDb(data_store, "Data Store 🔵", "PostgreSQL+pgvector / S3 / Kafka (MSK)", "Face embedding store; video storage; visitor event stream; recognition audit log")
    }

    System_Ext(rekognition, "AWS Rekognition")
    System_Ext(auth0, "Auth0")
    System_Ext(stripe, "Stripe")
    System_Ext(fcm, "Firebase FCM")
    System_Ext(iot_core, "AWS IoT Core")

    Rel(consumer, novadoor_fw, "Physical interaction with door")
    Rel(consumer, notification, "Receives visitor alerts", "Push/Email/SMS")

    Rel(novadoor_fw, iot_core, "MQTT — visitor events, telemetry", "TLS")
    Rel(iot_core, device_mgmt, "Routes device messages")
    Rel(device_mgmt, iot_core, "Lock/unlock commands → device", "MQTT")

    Rel(device_mgmt, data_store, "Visitor event stream", "Kafka")
    Rel(device_mgmt, notification, "Door state events", "SNS")

    Rel(data_store, face_recog, "Visitor face frames", "Kafka")
    Rel(face_recog, data_store, "Read/write face embeddings (pgvector)")
    Rel(face_recog, rekognition, "Cloud recognition (default mode)", "HTTPS")
    Rel(face_recog, access_rules, "Recognition results", "SNS")
    Rel(face_recog, notification, "Recognition results for alerts", "SNS")

    Rel(access_rules, device_mgmt, "Lock/unlock commands")
    Rel(access_rules, notification, "Rule-triggered alerts", "SNS")

    Rel(notification, monolith, "⚠️ Reads notification preferences from monolith DB", "PostgreSQL")

    Rel(identity, auth0, "OAuth2/OIDC")
    Rel(stripe, monolith, "Subscription billing (legacy path)")
    Rel(notification, fcm, "Push notifications")

    Rel(novadoor_fw, face_recog, "Edge recognition results (privacy mode)", "MQTT/IoT Core")
```

---

## Key Observations

### 1. The Monolith as a Hidden Hub
Despite being "MIGRATING," the Legacy Monolith has a dangerous hidden connection: the Notification Service **reads notification preferences from the monolith database**. This means a monolith failure disrupts visitor alerts — the primary consumer value proposition — even though the two services appear separate.

### 2. AWS Rekognition: No Abstraction Layer
The Facial Recognition Service sends face frames directly to AWS Rekognition with no abstraction layer. Any API change, outage, or pricing change hits the core product feature immediately. All biometric data processed in cloud mode crosses into AWS's systems.

### 3. The Auto-Unlock Chain
The critical path for auto-unlock is: **Facial Recognition → Access Rules Engine → Device Management → NovaDoor**. All four components must be working. They are owned by different teams and affected by different stressors, yet there is no end-to-end SLO for this chain.

### 4. Lock Commands Have No Fallback
If AWS IoT Core is unavailable, lock/unlock commands from the app or access rules cannot reach the device. The NovaDoor in edge mode can still execute locally-stored rules, but remote unlock is unavailable.
