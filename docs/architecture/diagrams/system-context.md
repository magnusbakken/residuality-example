# System Context Diagram (C4 Level 1)

This diagram shows NovaMesh as a single system and its relationships with external users and third-party systems.

```mermaid
C4Context
    title NovaMesh — System Context

    Person(consumer, "Consumer", "Homeowner or renter using NovaDoor for residential access control")
    Person(enterprise_admin, "Enterprise Admin", "Facility manager or IT admin managing NovaDoor across a building or campus")
    Person(support_agent, "Support Agent", "NovaMesh customer support staff using internal tooling")
    Person(engineer, "NovaMesh Engineer", "Develops, deploys, and operates the NovaMesh platform")

    System(novamesh, "NovaMesh Platform", "Smart door platform: AI-powered facial recognition, door lock control, visitor management, video storage, and access rules")

    System_Ext(novadoor_hw, "NovaDoor Device", "Physical smart door unit: 4K camera, electronic lock, edge AI processor, microphone/speaker")
    System_Ext(auth0, "Auth0", "Identity provider: OAuth2/OIDC, MFA")
    System_Ext(stripe, "Stripe", "Payment processing and subscription billing")
    System_Ext(shopify, "Shopify", "Hardware e-commerce storefront")
    System_Ext(rekognition, "AWS Rekognition", "Cloud facial recognition API (default mode)")
    System_Ext(fcm, "Firebase FCM", "Mobile push notifications — visitor alerts")
    System_Ext(sendgrid, "SendGrid", "Transactional email")
    System_Ext(twilio, "Twilio", "SMS notifications")
    System_Ext(zendesk, "Zendesk", "Customer support ticketing")
    System_Ext(alexa, "Amazon Alexa", "Voice assistant integration")
    System_Ext(google_home, "Google Home", "Smart home integration")
    System_Ext(homekit, "Apple HomeKit", "Smart home integration")

    Rel(consumer, novamesh, "Views live camera, receives visitor alerts, enrolls faces, configures access rules, unlocks door remotely", "HTTPS / Mobile App")
    Rel(enterprise_admin, novamesh, "Manages multi-door access, visitor clearances, access schedules, audit logs", "HTTPS / Web App")
    Rel(support_agent, novamesh, "Handles support tickets, accesses device state and account data", "HTTPS / Admin Portal")
    Rel(engineer, novamesh, "Develops features, deploys services, monitors infrastructure", "SSH / GitHub / Datadog")

    Rel(novadoor_hw, novamesh, "Streams visitor events (face frames, motion), receives lock/unlock commands", "MQTT over TLS")
    Rel(novamesh, novadoor_hw, "OTA firmware updates, lock/unlock commands, configuration sync", "MQTT / IoT Core")

    Rel(novamesh, auth0, "Authenticates users, issues tokens", "HTTPS")
    Rel(novamesh, stripe, "Processes payments, manages subscriptions", "HTTPS")
    Rel(novamesh, shopify, "Receives hardware purchase webhooks", "HTTPS")
    Rel(novamesh, rekognition, "Sends face frames for recognition (cloud mode)", "HTTPS")
    Rel(novamesh, fcm, "Delivers real-time visitor push alerts to mobile", "HTTPS")
    Rel(novamesh, sendgrid, "Sends email notifications and billing communications", "HTTPS")
    Rel(novamesh, twilio, "Sends SMS alerts", "HTTPS")
    Rel(novamesh, zendesk, "Manages support tickets, routes chatbot escalations", "HTTPS")
    Rel(novamesh, alexa, "Exposes door state and visitor query via Alexa skill", "HTTPS")
    Rel(novamesh, google_home, "Exposes door control via Google Home integration", "HTTPS")
    Rel(novamesh, homekit, "Exposes door lock via HomeKit bridge on NovaDoor", "Local/HTTPS")
```

---

## Context Narrative

NovaMesh sits at the intersection of **physical hardware** and **cloud intelligence**. The NovaDoor device is the primary data source — it captures video frames, detects motion and persons, and executes lock/unlock commands. The cloud platform processes those frames (via AI), applies access rules, delivers notifications, and manages subscriptions and billing.

### Three User Types

| User | Interaction Pattern | Key Concern |
|---|---|---|
| **Consumer** | Mobile-first; real-time alerts for visitor events; periodic face enrollment and rule configuration | Speed of alert delivery; accuracy of recognition; privacy of face data |
| **Enterprise Admin** | Web dashboard; managing tens or hundreds of NovaDoor units across sites; compliance reporting | Multi-site management; audit trails; uptime SLAs; tenant isolation |
| **Support Agent** | Internal admin portal; reading device state and account data; resolving customer issues | Access to live device status; subscription state visibility |

### External Dependency Risk Summary

| System | Dependency Type | Risk Level | Notes |
|---|---|---|---|
| AWS Rekognition | Core product feature (face recognition, cloud mode) | **Very High** | NovaMesh's primary differentiator depends on this; no abstraction layer; biometric data leaves NovaMesh systems |
| Auth0 | Core authentication | High | All authenticated requests depend on this; outage = platform inaccessible |
| Stripe | Revenue-critical | High | Subscription renewals and billing flow through this |
| Firebase FCM | Visitor alert delivery | Medium | Delays or failures affect the primary consumer value proposition (knowing who's at the door) |
| NovaDoor Device | Physical hardware | High | Product doesn't function without working hardware; OTA failures have physical safety implications |
