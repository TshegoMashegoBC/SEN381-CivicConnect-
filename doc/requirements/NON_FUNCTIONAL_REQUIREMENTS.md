# Non-Functional Requirements

## Overview

All NFRs are derived from stakeholder needs and the project's constraints identified in the [Constraints Analysis](./CONSTRAINTS_ANALYSIS.md) document. Each NFR is measurable—written with specific targets and includes acceptance criteria.

---

## NFR-001: Response Time (Performance)

| Attribute | Detail |
|-----------|--------|
| **ID** | NFR-001 |
| **Description** | The requester dashboard must load within 2 seconds when accessed by a user on a standard broadband connection. |
| **Source** | Requester needs: "Submit requests, track status, view history" — requesters need a responsive system. |
| **Priority** | High |
| **Measurement** | Use Chrome DevTools Network tab to measure Time to First Byte (TTFB) and full-page load time. Test with 3 representative pages: Dashboard, Request List, and Request Detail. Run 10 consecutive measurements. |
| **Acceptance Criteria** | AC-NFR-001: Dashboard page loads in < 2 seconds in 95% of test runs (10 consecutive measurements). |
| **How It Constrains M2 Architecture** | Forces the team to consider caching, database indexing, efficient API design (avoid N+1 queries), and server-side rendering vs. client-side rendering trade-offs. Technology choice must support these patterns. |
| **Downstream Risk** | If the selected frontend framework is heavy, or the database is not indexed, performance will fail. Requires performance testing in M3. If free-tier database performance is insufficient, may need to increase threshold to 3 seconds or implement more aggressive caching. |

---

## NFR-002: Concurrent Users (Scalability)

| Attribute | Detail |
|-----------|--------|
| **ID** | NFR-002 |
| **Description** | The system must support at least 50 concurrent users without significant degradation. "Significant degradation" defined as response time > 4 seconds on the dashboard. |
| **Source** | Staff and Management needs from stakeholder analysis: "Multiple staff working simultaneously" — the system must handle real-world usage. |
| **Priority** | Medium |
| **Measurement** | Use Apache JMeter or k6 to simulate 50 concurrent users performing typical actions (view dashboard, submit request, update status) over a 5-minute period. |
| **Acceptance Criteria** | AC-NFR-002: All requests complete successfully (HTTP 200) with average response time < 3 seconds and maximum < 5 seconds. |
| **How It Constrains M2 Architecture** | Forces the team to consider database connection pooling, stateless application design for horizontal scaling, and load balancing. Technology must support multiple concurrent connections (e.g., Node.js event loop, or Java thread pooling). |
| **Downstream Risk** | If the team chooses a framework with poor concurrency support (e.g., PHP without Apache tuning), or a database with low connection limits (e.g., free-tier PostgreSQL with 20 connections), this NFR will fail. Free-tier limitations are a key concern (see Trade-Off 2 in Constraints Analysis). |

---

## NFR-003: Data Encryption (Security)

| Attribute | Detail |
|-----------|--------|
| **ID** | NFR-003 |
| **Description** | All data transmitted between the client and server must be encrypted using TLS 1.2 or higher. |
| **Source** | Security constraint from Master Project Brief (SE-001): "Security is a lifecycle-wide responsibility" and SE-003: "Data in transit must be encrypted." |
| **Priority** | High |
| **Measurement** | Use SSL Labs SSL Server Test (or equivalent tool) to verify the deployed site uses TLS 1.2+ and has no known vulnerabilities (e.g., POODLE, Heartbleed). |
| **Acceptance Criteria** | AC-NFR-003: SSL Labs test returns an overall rating of "A" or "A+". |
| **How It Constrains M2 Architecture** | Forces the team to choose a deployment platform that provides free SSL certificates. Cannot use plain HTTP. Must enforce HTTPS redirection at the application or server level. |
| **Downstream Risk** | If the selected platform doesn't support automatic SSL (e.g., some free hosting services), the team must manually configure certificates, creating an operational burden. |

---

## NFR-004: Audit Logging (Accountability)

| Attribute | Detail |
|-----------|--------|
| **ID** | NFR-004 |
| **Description** | All status changes on service requests must be logged with: timestamp, user ID, previous status, new status, and optional comment. |
| **Source** | Staff and Management needs from stakeholder analysis: "Accountability" and the Master Project Brief's problem statement about "weak accountability for changes to request status." |
| **Priority** | High |
| **Measurement** | Manually verify audit log entries in the database after performing status transitions. Query the audit table to check completeness of fields. |
| **Acceptance Criteria** | AC-NFR-004: After 5 distinct status transitions, the audit log contains 5 entries with all required fields populated (timestamp, user ID, previous status, new status). |
| **How It Constrains M2 Architecture** | Forces the team to design the database schema to support an audit log table. The architecture must ensure that every status update triggers an audit record — this may be implemented via database triggers, ORM hooks, or explicit service-layer logic. Technology choice must support database transactions to ensure atomicity (status update and audit log must succeed or fail together). |
| **Downstream Risk** | If the team forgets to implement audit logging early, retrofitting it later will be difficult and error prone. Must be designed in M2 and implemented in M3. |

---

## NFR-005: Availability (Reliability)

| Attribute | Detail |
|-----------|--------|
| **ID** | NFR-005 |
| **Description** | The system must be available 99.5% of the time during business hours. |
| **Source** | Staff and Management needs from stakeholder analysis: "Monitor and analyse performance" — you can't monitor or analyse if the system is down. |
| **Priority** | Medium |
| **Measurement** | Monitor uptime using UptimeRobot or similar tool over a 2-week period during business hours. |
| **Acceptance Criteria** | AC-NFR-005: Uptime >= 99.5% (maximum 43 minutes of downtime per month during business hours) over a 2-week test period. |
| **How It Constrains M2 Architecture** | Forces the team to consider: deployment platform reliability (choose a reputable cloud provider with good SLAs), error handling (graceful failure), database backup/recovery, and health check endpoints for monitoring. Technology should support hot-reload or zero-downtime deployment if possible. |
| **Downstream Risk** | Free-tier platforms may have less reliability (e.g., Heroku dyno sleeps after 30 minutes idle). The team may need to choose a platform with better uptime guarantees, potentially incurring cost (see Trade-Off 2 in Constraints Analysis). |

---

## NFR-006: Accessibility (Usability)

| Attribute | Detail |
|-----------|--------|
| **ID** | NFR-006 |
| **Description** | The web interface must meet basic WCAG 2.1 Level A accessibility standards. |
| **Source** | Organisation's inclusivity policy (assumed/implicit quality expectation) and good engineering practice. |
| **Priority** | Low |
| **Measurement** | Use automated accessibility checker (e.g., axe DevTools, Lighthouse) to scan all major pages: Dashboard, Request Form, Request List, and Request Detail pages. |
| **Acceptance Criteria** | AC-NFR-006: No Level A violations on Dashboard, Request Form, Request List, and Request Detail pages as reported by axe DevTools. |
| **How It Constrains M2 Architecture** | Forces the team to choose a frontend framework with good accessibility support (e.g., semantic HTML, ARIA attributes). Design decisions must consider colour contrast, keyboard navigation, and screen reader support. |
| **Downstream Risk** | If the team uses a UI library that doesn't prioritise accessibility (e.g., some custom component libraries), they'll need to manually add ARIA roles, increasing development time. |

---

## Summary

| Priority | NFRs |
|----------|------|
| **High** | NFR-001 (Response Time), NFR-003 (Encryption), NFR-004 (Audit Logging) |
| **Medium** | NFR-002 (Concurrent Users), NFR-005 (Availability) |
| **Low** | NFR-006 (Accessibility) |

---
