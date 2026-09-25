# Architecturally Significant Requirements (ASRs)

**Document ID:** ASR-M2
**Version:** 1.0 (draft)
**Status:** For M2 architecture baseline
**Owner:** Engineering Larpers
**Derived from:** PED v1.0 (M1 baseline), NFR-001 – NFR-006, Constraints SC/SH/CR/QU/SE
**Feeds into:** ADR-001 – ADR-004, Architecture Diagrams, RTM (M2 design links)
**Last updated:** [YYYY-MM-DD](2026-09-23)

---

## 1. Purpose

This document identifies and records the **Architecturally Significant Requirements (ASRs)** for CivicConnect — those requirements from the M1 baseline that materially shape architecture, data, technology, and design decisions.

ASRs are not a re-statement of the full requirements set. They are the subset of functional requirements, non-functional requirements, and constraints whose satisfaction **constrains or drives** one or more architecture decisions. Each ASR:

- traces to one or more upstream baseline artefacts (NFR / FR / Constraint),
- states a **measurable expectation** (so it can be tested in M3),
- names its **architecture influence** (what it forces the architecture to do),
- links to the **ADR(s)** it motivates, and
- carries a short **engineering rationale** explaining *why* it matters.

ASRs not satisfied by the M2 architecture become risks in the M2 Risk Register.

---

## 2. Summary Table

| ID      | ASR                                        | Source                          | Linked ADR(s)                | Priority |
|---------|--------------------------------------------|---------------------------------|------------------------------|----------|
| ASR-001 | Audit Logging for Accountability           | NFR-004, Stakeholder (Mgmt)     | ADR-003 (Data Persistence)   | High     |
| ASR-002 | Concurrent User Support                    | NFR-002, Stakeholder (Staff/Mgmt) | ADR-001, ADR-004           | High     |
| ASR-003 | Response Time (Performance)                | NFR-001, Stakeholder (Requester)| ADR-001, ADR-004             | High     |
| ASR-004 | Data Encryption in Transit                 | NFR-003, Constraint SE-003      | ADR-002 (Deployment Platform)| High     |
| ASR-005 | Role-Based Access Control (RBAC)           | Constraint SE-004, FR-003/004/007 | ADR-001, ADR-004           | High     |
| ASR-006 | Availability During Business Hours         | NFR-005, Stakeholder (Staff/Mgmt) | ADR-002                    | Medium   |

---

## 3. ASRs

### ASR-001: Audit Logging for Accountability

| Field | Detail |
|---|---|
| **ID** | ASR-001 |
| **Source** | NFR-004; Stakeholder Need (Management accountability) |
| **Description** | All status changes on service requests must be logged with timestamp, user ID, previous status, new status, and optional comment. |
| **Measurable Expectation** | After 5 distinct status transitions, the audit log contains 5 entries with all required fields populated. |
| **Architecture Influence** | Forces database schema to include an audit log table. Requires transactional integrity between status update and audit record. Necessitates service-layer logic or database triggers. |
| **Linked ADR** | ADR-003 (Data Persistence) |

**Engineering Rationale:** The M1 problem statement explicitly identifies "weak accountability for changes to request status" as a core problem. NFR-004 makes this measurable. The architecture must guarantee atomicity — either both the status change and audit log succeed, or neither does. This constrains the choice of persistence technology (must support transactions) and the design of the status update workflow (service-layer orchestration required).

---

### ASR-002: Concurrent User Support

| Field | Detail |
|---|---|
| **ID** | ASR-002 |
| **Source** | NFR-002; Stakeholder Need (Staff/Management simultaneous use) |
| **Description** | The system must support at least 50 concurrent users without significant degradation (response time > 4 seconds on dashboard). |
| **Measurable Expectation** | 50 concurrent users performing typical actions: all requests HTTP 200, average response < 3 seconds, maximum < 5 seconds. |
| **Architecture Influence** | Forces stateless application design for horizontal scaling. Requires database connection pooling. Constrains technology choice to frameworks with proven concurrency support. |
| **Linked ADR** | ADR-001 (Architecture Style), ADR-004 (Technology Stack) |

**Engineering Rationale:** CivicConnect is a multi-user platform where staff and management will access the system simultaneously. A 3-person team building a monolithic application with blocking I/O may fail this NFR. The architecture must either support horizontal scaling (paid infrastructure) or use efficient concurrency patterns within a single instance (connection pooling, async I/O, efficient ORM usage).

---

### ASR-003: Response Time (Performance)

| Field | Detail |
|---|---|
| **ID** | ASR-003 |
| **Source** | NFR-001; Stakeholder Need (Requester dashboard responsiveness) |
| **Description** | The requester dashboard must load within 2 seconds on standard broadband. |
| **Measurable Expectation** | Dashboard loads in < 2 seconds in 95% of 10 consecutive test runs. |
| **Architecture Influence** | Forces consideration of caching, database indexing, efficient API design (avoid N+1 queries), and rendering strategy (SSR vs. CSR trade-offs). |
| **Linked ADR** | ADR-001 (Architecture Style), ADR-004 (Technology Stack) |

**Engineering Rationale:** A slow dashboard erodes requester trust, which is the primary stakeholder concern. The architecture must avoid common performance anti-patterns: unindexed queries, excessive client-side rendering weight, and chatty API designs. This influences both the data layer (indexing strategy) and the presentation layer (rendering approach).

---

### ASR-004: Data Encryption in Transit

| Field | Detail |
|---|---|
| **ID** | ASR-004 |
| **Source** | NFR-003; Constraint SE-003 |
| **Description** | All data transmitted between client and server must be encrypted using TLS 1.2 or higher. |
| **Measurable Expectation** | SSL Labs test returns overall rating of "A" or "A+". |
| **Architecture Influence** | Forces deployment platform to provide free SSL certificates. Requires HTTPS enforcement at application or server level. Eliminates platforms without automatic SSL support. |
| **Linked ADR** | ADR-002 (Deployment Platform) |

**Engineering Rationale:** Security is a lifecycle-wide responsibility per Master Brief §16. The deployment platform choice is directly constrained by this ASR — a platform without free SSL (or with complex SSL configuration) adds operational burden and risk. The architecture must enforce HTTPS redirects and cannot use plain HTTP.

---

### ASR-005: Role-Based Access Control (RBAC)

| Field | Detail |
|---|---|
| **ID** | ASR-005 |
| **Source** | Constraint SE-004; FR-003, FR-004, FR-007 |
| **Description** | Authentication and Role-Based Access Control are mandatory. Three roles identified: Requester, Staff, Management. |
| **Measurable Expectation** | Unauthorized users cannot access restricted endpoints; staff cannot view management dashboards; requesters can only view their own requests. |
| **Architecture Influence** | Forces authentication mechanism design from the start. Requires middleware/interceptor for authorization. Shapes data model (user roles, permissions). |
| **Linked ADR** | ADR-001 (Architecture Style), ADR-004 (Technology Stack) |

**Engineering Rationale:** CivicConnect handles service requests with different visibility requirements: requesters see their own requests, staff see relevant requests, management sees aggregate data. RBAC cannot be bolted on later — it must be designed into the architecture from M2. The authentication mechanism (session-based vs. token-based) affects statelessness (ASR-002) and technology choice.

---

### ASR-006: Availability During Business Hours

| Field | Detail |
|---|---|
| **ID** | ASR-006 |
| **Source** | NFR-005; Stakeholder Need (Staff/Management monitoring) |
| **Description** | System must be available 99.5% of the time during business hours. |
| **Measurable Expectation** | Uptime >= 99.5% (max 43 minutes downtime per month during business hours) over a 2-week test period. |
| **Architecture Influence** | Forces consideration of deployment platform reliability, error handling (graceful failure), database backup/recovery, health check endpoints. |
| **Linked ADR** | ADR-002 (Deployment Platform) |

**Engineering Rationale:** Free-tier platforms often have reliability limitations (e.g., cold starts, dyno sleeping). The architecture must either select a platform with acceptable uptime for free tier or accept the risk explicitly. Health check endpoints and graceful degradation patterns must be designed, not added later.

---

## 4. Change Log

| Version | Date | Author | Change |
|---------|------|--------|--------|
| 1.0 (draft) | [YYYY-MM-DD](2026-09-23) | Katlego Lethole | Initial ASR extraction from M1 baseline |
