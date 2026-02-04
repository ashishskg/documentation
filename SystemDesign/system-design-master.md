# System Design Master (Senior Engineer / Tech Lead)

## How to Use This Document
- Use this file as the single source of truth.
- For each question, fill all template sections.
- Diagrams are referenced as images under `diagrams/`.

## Export (DOCX/PDF)
See the **Export Commands** section at the end of this file.

---

## Table of Contents

### A. Foundations / Infra
1. [Design a URL Shortener](#q01-design-a-url-shortener)
2. [Design a Rate Limiter (per-user + per-IP)](#q02-design-a-rate-limiter-per-user--per-ip)
3. [Design an API Gateway + AuthN/AuthZ (JWT/OAuth2)](#q03-design-an-api-gateway--authnauthz-jwtoauth2)
4. [Design a Feature Flag / Config Service](#q04-design-a-feature-flag--config-service)
5. [Design a Logging Platform (collection → storage → search)](#q05-design-a-logging-platform-collection--storage--search)
6. [Design a Metrics/Monitoring Platform (ingestion + aggregation)](#q06-design-a-metricsmonitoring-platform-ingestion--aggregation)

### B. Kafka / Event-Driven Systems
7. [Design a Notification System (email/SMS/push; retries/DLQ)](#q07-design-a-notification-system-emailsmspush-retriesdlq)
8. [Design an Event Ingestion Platform (Kafka + schema + consumers)](#q08-design-an-event-ingestion-platform-kafka--schema--consumers)
9. [Design an Order Processing Pipeline (Kafka choreography)](#q09-design-an-order-processing-pipeline-kafka-choreography)
10. [Design Outbox + CDC Integration Pattern](#q10-design-outbox--cdc-integration-pattern)
11. [Design Payment Events + Reconciliation Pipeline](#q11-design-payment-events--reconciliation-pipeline)
12. [Design a Fraud Detection Stream (near real-time scoring)](#q12-design-a-fraud-detection-stream-near-real-time-scoring)

### C. Transactional / Consistency-Heavy
13. [Design an E-commerce Checkout + Order System (Saga)](#q13-design-an-e-commerce-checkout--order-system-saga)
14. [Design a Wallet / Ledger System](#q14-design-a-wallet--ledger-system)
15. [Design a Ticket Booking System (avoid oversell)](#q15-design-a-ticket-booking-system-avoid-oversell)
16. [Design an Inventory System (reservations)](#q16-design-an-inventory-system-reservations)
17. [Design a Subscription Billing System](#q17-design-a-subscription-billing-system)
18. [Design a Password Reset / Account Recovery System](#q18-design-a-password-reset--account-recovery-system)

### D. Real-time / Low Latency
19. [Design a Chat System (WebSocket)](#q19-design-a-chat-system-websocket)
20. [Design a Presence/Last-Seen System](#q20-design-a-presencelast-seen-system)
21. [Design a Real-time Collaboration System (high-level)](#q21-design-a-real-time-collaboration-system-high-level)
22. [Design Ride Dispatch/Matching (geo + scaling)](#q22-design-ride-dispatchmatching-geo--scaling)
23. [Design a Real-time Leaderboard](#q23-design-a-real-time-leaderboard)

### E. Product Scale Systems
24. [Design a Feed/Timeline](#q24-design-a-feedtimeline)
25. [Design Media Upload + CDN Serving](#q25-design-media-upload--cdn-serving)
26. [Design Search for Marketplace](#q26-design-search-for-marketplace)
27. [Design Recommendation Serving (high-level)](#q27-design-recommendation-serving-high-level)
28. [Design Location-based Nearby Search (geo index)](#q28-design-location-based-nearby-search-geo-index)
29. [Design Analytics Clickstream Pipeline](#q29-design-analytics-clickstream-pipeline)
30. [Design Multi-tenant SaaS Platform Baseline](#q30-design-multi-tenant-saas-platform-baseline)

### Appendices
- [Cross-cutting Cheat Sheets](#cross-cutting-cheat-sheets)
- [Diagram Conventions](#diagram-conventions)
- [Export Commands](#export-commands)

---

## Standard Template (Use for Every Question)

### 1) Problem Statement

### 2) Features / Functional Requirements

### 3) Non-Functional Requirements
- Availability:
- Latency (p95/p99):
- Scale (DAU/MAU, peak QPS):
- Consistency:
- Durability:
- Cost constraints:

### 4) Assumptions + Back-of-the-envelope Sizing
- Peak QPS:
- Storage/day:
- Bandwidth:
- Hot keys / hotspots:

### 5) High-Level Architecture

### 6) APIs

### 7) Data Model

### 8) Kafka / Async Design (if applicable)
- Topics (names, partitions, retention):
- Ordering guarantees:
- Consumer groups:
- Retry strategy:
- DLQ:
- Idempotency/dedup:
- Schema evolution:

### 9) Diagrams
- Architecture diagram:
- Key flow diagram (sequence):

### 10) Key Flows
- Write path:
- Read path:
- Async path:

### 11) Advantages

### 12) Disadvantages / Risks

### 13) Tradeoffs + Alternatives

### 14) Failure Modes + Mitigations

### 15) Observability (SLIs/SLOs)
- Metrics:
- Logs:
- Traces:
- Alerts:

### 16) Security + Compliance
- AuthN/AuthZ:
- PII handling:
- Encryption:
- Auditing:

### 17) Kubernetes Deployment View
- Workloads:
- HPA:
- Requests/limits:
- Probes:
- Rollout strategy:
- PDB:

### 18) Capacity Planning + Cost

### 19) Evolution Plan (v1 → v2 → v3)

---

# Questions

## Q01: Design a URL Shortener
<a id="q01-design-a-url-shortener"></a>

[Fill using the Standard Template]

**Diagrams**

![Q01 Architecture](diagrams/q01_arch.png)

![Q01 Key Flow](diagrams/q01_seq.png)

---

## Q02: Design a Rate Limiter (per-user + per-IP)
<a id="q02-design-a-rate-limiter-per-user--per-ip"></a>

[Fill using the Standard Template]

**Diagrams**

![Q02 Architecture](diagrams/q02_arch.png)

![Q02 Key Flow](diagrams/q02_seq.png)

---

## Q03: Design an API Gateway + AuthN/AuthZ (JWT/OAuth2)
<a id="q03-design-an-api-gateway--authnauthz-jwtoauth2"></a>

[Fill using the Standard Template]

**Diagrams**

![Q03 Architecture](diagrams/q03_arch.png)

![Q03 Key Flow](diagrams/q03_seq.png)

---

## Q04: Design a Feature Flag / Config Service
<a id="q04-design-a-feature-flag--config-service"></a>

[Fill using the Standard Template]

**Diagrams**

![Q04 Architecture](diagrams/q04_arch.png)

![Q04 Key Flow](diagrams/q04_seq.png)

---

## Q05: Design a Logging Platform (collection → storage → search)
<a id="q05-design-a-logging-platform-collection--storage--search"></a>

[Fill using the Standard Template]

**Diagrams**

![Q05 Architecture](diagrams/q05_arch.png)

![Q05 Key Flow](diagrams/q05_seq.png)

---

## Q06: Design a Metrics/Monitoring Platform (ingestion + aggregation)
<a id="q06-design-a-metricsmonitoring-platform-ingestion--aggregation"></a>

[Fill using the Standard Template]

**Diagrams**
- `diagrams/q06_arch.png`
- `diagrams/q06_seq.png`

---

## Q07: Design a Notification System (email/SMS/push; retries/DLQ)
<a id="q07-design-a-notification-system-emailsmspush-retriesdlq"></a>

[Fill using the Standard Template]

**Diagrams**
- `diagrams/q07_arch.png`
- `diagrams/q07_seq.png`

---

## Q08: Design an Event Ingestion Platform (Kafka + schema + consumers)
<a id="q08-design-an-event-ingestion-platform-kafka--schema--consumers"></a>

[Fill using the Standard Template]

**Diagrams**
- `diagrams/q08_arch.png`
- `diagrams/q08_seq.png`

---

## Q09: Design an Order Processing Pipeline (Kafka choreography)
<a id="q09-design-an-order-processing-pipeline-kafka-choreography"></a>

[Fill using the Standard Template]

**Diagrams**
- `diagrams/q09_arch.png`
- `diagrams/q09_seq.png`

---

## Q10: Design Outbox + CDC Integration Pattern
<a id="q10-design-outbox--cdc-integration-pattern"></a>

[Fill using the Standard Template]

**Diagrams**
- `diagrams/q10_arch.png`
- `diagrams/q10_seq.png`

---

## Q11: Design Payment Events + Reconciliation Pipeline
<a id="q11-design-payment-events--reconciliation-pipeline"></a>

[Fill using the Standard Template]

**Diagrams**
- `diagrams/q11_arch.png`
- `diagrams/q11_seq.png`

---

## Q12: Design a Fraud Detection Stream (near real-time scoring)
<a id="q12-design-a-fraud-detection-stream-near-real-time-scoring"></a>

[Fill using the Standard Template]

**Diagrams**
- `diagrams/q12_arch.png`
- `diagrams/q12_seq.png`

---

## Q13: Design an E-commerce Checkout + Order System (Saga)
<a id="q13-design-an-e-commerce-checkout--order-system-saga"></a>

[Fill using the Standard Template]

**Diagrams**
- `diagrams/q13_arch.png`
- `diagrams/q13_seq.png`

---

## Q14: Design a Wallet / Ledger System
<a id="q14-design-a-wallet--ledger-system"></a>

[Fill using the Standard Template]

**Diagrams**
- `diagrams/q14_arch.png`
- `diagrams/q14_seq.png`

---

## Q15: Design a Ticket Booking System (avoid oversell)
<a id="q15-design-a-ticket-booking-system-avoid-oversell"></a>

[Fill using the Standard Template]

**Diagrams**
- `diagrams/q15_arch.png`
- `diagrams/q15_seq.png`

---

## Q16: Design an Inventory System (reservations)
<a id="q16-design-an-inventory-system-reservations"></a>

[Fill using the Standard Template]

**Diagrams**
- `diagrams/q16_arch.png`
- `diagrams/q16_seq.png`

---

## Q17: Design a Subscription Billing System
<a id="q17-design-a-subscription-billing-system"></a>

[Fill using the Standard Template]

**Diagrams**
- `diagrams/q17_arch.png`
- `diagrams/q17_seq.png`

---

## Q18: Design a Password Reset / Account Recovery System
<a id="q18-design-a-password-reset--account-recovery-system"></a>

[Fill using the Standard Template]

**Diagrams**
- `diagrams/q18_arch.png`
- `diagrams/q18_seq.png`

---

## Q19: Design a Chat System (WebSocket)
<a id="q19-design-a-chat-system-websocket"></a>

[Fill using the Standard Template]

**Diagrams**
- `diagrams/q19_arch.png`
- `diagrams/q19_seq.png`

---

## Q20: Design a Presence/Last-Seen System
<a id="q20-design-a-presencelast-seen-system"></a>

[Fill using the Standard Template]

**Diagrams**
- `diagrams/q20_arch.png`
- `diagrams/q20_seq.png`

---

## Q21: Design a Real-time Collaboration System (high-level)
<a id="q21-design-a-real-time-collaboration-system-high-level"></a>

[Fill using the Standard Template]

**Diagrams**
- `diagrams/q21_arch.png`
- `diagrams/q21_seq.png`

---

## Q22: Design Ride Dispatch/Matching (geo + scaling)
<a id="q22-design-ride-dispatchmatching-geo--scaling"></a>

[Fill using the Standard Template]

**Diagrams**
- `diagrams/q22_arch.png`
- `diagrams/q22_seq.png`

---

## Q23: Design a Real-time Leaderboard
<a id="q23-design-a-real-time-leaderboard"></a>

[Fill using the Standard Template]

**Diagrams**
- `diagrams/q23_arch.png`
- `diagrams/q23_seq.png`

---

## Q24: Design a Feed/Timeline
<a id="q24-design-a-feedtimeline"></a>

[Fill using the Standard Template]

**Diagrams**
- `diagrams/q24_arch.png`
- `diagrams/q24_seq.png`

---

## Q25: Design Media Upload + CDN Serving
<a id="q25-design-media-upload--cdn-serving"></a>

[Fill using the Standard Template]

**Diagrams**
- `diagrams/q25_arch.png`
- `diagrams/q25_seq.png`

---

## Q26: Design Search for Marketplace
<a id="q26-design-search-for-marketplace"></a>

[Fill using the Standard Template]

**Diagrams**
- `diagrams/q26_arch.png`
- `diagrams/q26_seq.png`

---

## Q27: Design Recommendation Serving (high-level)
<a id="q27-design-recommendation-serving-high-level"></a>

[Fill using the Standard Template]

**Diagrams**
- `diagrams/q27_arch.png`
- `diagrams/q27_seq.png`

---

## Q28: Design Location-based Nearby Search (geo index)
<a id="q28-design-location-based-nearby-search-geo-index"></a>

[Fill using the Standard Template]

**Diagrams**
- `diagrams/q28_arch.png`
- `diagrams/q28_seq.png`

---

## Q29: Design Analytics Clickstream Pipeline
<a id="q29-design-analytics-clickstream-pipeline"></a>

[Fill using the Standard Template]

**Diagrams**
- `diagrams/q29_arch.png`
- `diagrams/q29_seq.png`

---

## Q30: Design Multi-tenant SaaS Platform Baseline
<a id="q30-design-multi-tenant-saas-platform-baseline"></a>

[Fill using the Standard Template]

**Diagrams**
- `diagrams/q30_arch.png`
- `diagrams/q30_seq.png`

---

# Cross-cutting Cheat Sheets
<a id="cross-cutting-cheat-sheets"></a>

## Kafka Cheat Sheet

## Kubernetes Cheat Sheet

## Databases Cheat Sheet

## Caching Cheat Sheet

## SLO/SLI + Observability Cheat Sheet

## Security Cheat Sheet

---

# Diagram Conventions
<a id="diagram-conventions"></a>

## Naming
- `qNN_arch.png` for architecture
- `qNN_seq.png` for key flow/sequence

## Recommended Diagram Types
- Architecture: component diagram
- Flow: sequence diagram

---

# Export Commands
<a id="export-commands"></a>

## Folder Structure
- `system-design-master.md`
- `diagrams/`
- `assets/`
- `exports/`

## Pandoc (DOCX)
- Input: `system-design-master.md`
- Output: `exports/System_Design_Master.docx`

## Pandoc (PDF)
- Input: `system-design-master.md`
- Output: `exports/System_Design_Master.pdf`

## Diagram Generation (Mermaid → Images)
- Keep Mermaid source files under `diagrams-src/` (optional)
- Export images into `diagrams/`
