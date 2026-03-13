# Data Flow Diagrams

This document shows how data moves through the NovaMesh platform for key scenarios.

---

## Flow 1: Device Telemetry → AI Anomaly Detection → Alert

This is the primary real-time data flow driving NovaMesh's AI security feature.

```mermaid
sequenceDiagram
    participant Hub as NovaMesh Hub<br/>(Edge Device)
    participant IoT as AWS IoT Core<br/>(MQTT Broker)
    participant Kafka as Kafka (MSK)<br/>(Event Bus)
    participant Anomaly as Anomaly Detection<br/>Engine
    participant DataLake as Data Lake<br/>(S3 + Glue)
    participant Notify as Notification<br/>Service
    participant FCM as Firebase FCM
    participant App as Customer<br/>Mobile App

    Hub->>IoT: Telemetry event (MQTT/TLS)<br/>network stats, device health, traffic patterns
    IoT->>Kafka: IoT Rule → Kafka topic: device.telemetry
    Kafka->>Anomaly: Stream consumer reads telemetry batch
    Kafka->>DataLake: S3 Sink Connector → raw telemetry stored

    Note over Anomaly: Run ensemble inference:<br/>Isolation Forest + LSTM

    alt Anomaly Detected
        Anomaly->>Notify: Publish anomaly alert event<br/>(SNS topic: alerts.anomaly)
        Notify->>FCM: Push notification request
        FCM->>App: "Unusual network activity detected<br/>on your NovaMesh Hub"
    else No Anomaly
        Anomaly->>DataLake: Write inference result (normal)
    end

    Note over DataLake: Weekly batch retraining<br/>triggered manually
```

---

## Flow 2: Hardware Purchase → Subscription Activation

The post-purchase flow that onboards new hardware customers into the subscription platform.

```mermaid
sequenceDiagram
    participant Customer as Customer
    participant Shopify as Shopify<br/>(E-Commerce)
    participant StripeExt as Stripe<br/>(via Shopify)
    participant APIGW as API Gateway
    participant SubSvc as Subscription<br/>Service
    participant MonolithDB as Monolith DB<br/>(dual-write target)
    participant BillingDB as Billing DB
    participant Notify as Notification<br/>Service
    participant SendGrid as SendGrid

    Customer->>Shopify: Place hardware order
    Shopify->>StripeExt: Process payment
    StripeExt-->>Shopify: Payment confirmed
    Shopify->>APIGW: POST /webhooks/shopify/order-paid
    APIGW->>SubSvc: Route webhook
    SubSvc->>BillingDB: Create trial subscription record
    SubSvc->>MonolithDB: Dual-write subscription (migration period)

    Note over SubSvc,MonolithDB: ⚠️ Risk: if either write fails,<br/>subscription state becomes inconsistent

    SubSvc->>Notify: Publish event: subscription.trial.created
    Notify->>SendGrid: Send welcome email with app download link
    SendGrid-->>Customer: "Welcome to NovaMesh!"

    Note over Customer: Customer downloads app,<br/>provisions Hub via Bluetooth
```

---

## Flow 3: User Authentication & Feature Entitlement Check

```mermaid
sequenceDiagram
    participant App as Web / Mobile App
    participant APIGW as API Gateway<br/>(Lambda Auth)
    participant Identity as Identity<br/>Service
    participant Auth0 as Auth0<br/>(IdP)
    participant SubSvc as Subscription<br/>Service
    participant AIOrch as AI Orchestration<br/>Service

    App->>APIGW: Request to /api/v2/ai/assistant<br/>(Bearer token)
    APIGW->>Identity: Validate JWT (Lambda Authoriser)
    Identity->>Auth0: Verify token signature
    Auth0-->>Identity: Token valid, claims returned
    Identity-->>APIGW: Principal: {user_id, org_id, roles}

    APIGW->>AIOrch: Forward request + principal context
    AIOrch->>SubSvc: GET /entitlements/{user_id}?feature=ai_assistant
    SubSvc-->>AIOrch: {entitled: true, tier: "insights"}

    Note over AIOrch: User on "Insights" tier<br/>✓ AI Assistant enabled

    AIOrch->>AIOrch: Route to OpenAI GPT-4o
    AIOrch-->>App: AI Assistant response
```

---

## Flow 4: OTA Firmware Update

```mermaid
sequenceDiagram
    participant Engineer as NovaMesh<br/>Engineer
    participant CI as GitHub Actions<br/>CI/CD
    participant S3 as S3<br/>(Firmware Artefacts)
    participant DeviceSvc as Device Management<br/>Service
    participant IoT as AWS IoT Core
    participant Hub as NovaMesh Hub<br/>(Target Device)
    participant Shadow as IoT Device Shadow

    Engineer->>CI: Merge firmware PR → tag release
    CI->>S3: Upload signed firmware binary
    CI->>DeviceSvc: POST /firmware/releases (version, S3 url, target criteria)
    DeviceSvc->>DeviceSvc: Select target devices (firmware version criteria)
    DeviceSvc->>IoT: Publish OTA job to target fleet

    loop For each target Hub
        IoT->>Hub: Deliver OTA job notification (MQTT)
        Hub->>S3: Download firmware binary (HTTPS)
        Hub->>Hub: Verify signature, flash firmware
        alt Update Success
            Hub->>Shadow: Update shadow: {firmware_version: "2.4.1"}
            Shadow->>DeviceSvc: Shadow update event → mark device updated
        else Update Failed (~3-5% of attempts)
            Hub->>Shadow: Report error state
            Note over DeviceSvc: ⚠️ Manual intervention required<br/>No automated rollback
        end
    end
```

---

## Flow 5: AI Assistant — Natural Language Home Control

```mermaid
sequenceDiagram
    participant User as User
    participant App as Mobile App
    participant APIGW as API Gateway
    participant AIOrch as AI Orchestration<br/>Service
    participant OpenAI as OpenAI API<br/>(GPT-4o)
    participant DeviceSvc as Device Management<br/>Service
    participant Hub as NovaMesh Hub

    User->>App: "Turn off all lights at 10pm on weekdays"
    App->>APIGW: POST /api/v2/ai/assistant/chat
    APIGW->>AIOrch: Forward request
    AIOrch->>OpenAI: Chat completion with function definitions<br/>(device_command, schedule_automation)

    Note over OpenAI: Interprets intent,<br/>generates function call

    OpenAI-->>AIOrch: function_call: schedule_automation<br/>{days: [Mon-Fri], time: "22:00", action: "lights_off"}
    AIOrch->>DeviceSvc: POST /devices/{home_id}/automations
    DeviceSvc->>Hub: Deliver automation config (MQTT)
    Hub-->>DeviceSvc: Automation saved ACK

    AIOrch-->>App: "Done! I've set all lights to turn off at 10pm on weekdays."
    App-->>User: Display confirmation

    Note over AIOrch,OpenAI: ⚠️ No abstraction layer between<br/>AIOrch and OpenAI — direct dependency
```

---

## Flow 6: GDPR Data Deletion Request (Current State — Manual & Fragmented)

This flow illustrates a current **architectural gap** that is particularly relevant for stressor analysis.

```mermaid
flowchart TD
    A[Customer submits<br/>GDPR deletion request] --> B[Support Agent<br/>receives in Zendesk]
    B --> C{Manual triage}
    C --> D[Delete from Identity Service DB]
    C --> E[Delete from Monolith DB]
    C --> F[Delete from Billing DB]
    C --> G[Request HubSpot deletion<br/>manually]
    C --> H[Request Customer.io<br/>deletion manually]
    C --> I[Notify Zendesk to anonymise<br/>ticket data]
    C --> J[Data Lake — no deletion<br/>capability yet]
    C --> K[Snowflake — no deletion<br/>capability yet]

    D & E & F & G & H & I --> L{All confirmed?}
    J & K --> M[⚠️ Data remains in<br/>Data Lake & Warehouse<br/>indefinitely]
    L -->|Sometimes| N[Close Zendesk ticket]
    L -->|Often| O[⚠️ Partial deletion only<br/>some systems missed]

    style M fill:#ff9999
    style O fill:#ff9999
    style J fill:#ffcccc
    style K fill:#ffcccc
```

> This flow is a significant regulatory risk. A single automation service and event-driven deletion pipeline is on the roadmap but not yet designed.
