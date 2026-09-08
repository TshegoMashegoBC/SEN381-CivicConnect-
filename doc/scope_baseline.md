# Scope Baseline

## 1. In-Scope

All functional requirements identified are committed to the M1 baseline:

| ID | Capability | FR Link | Priority |
|----|------------|---------|----------|
| S-001 | Request Submission | FR-001 | High |
| S-002 | Status Tracking | FR-002 | High |
| S-003 | Staff Request Queue | FR-003 | High |
| S-004 | Request Assignment | FR-004 | High |
| S-005 | Status Transitions | FR-005 | High |
| S-006 | Action/Comments Logging | FR-006 | Medium |
| S-007 | Management Monitoring | FR-007 | Medium |

---

## 2. Out-of-Scope

| ID | Exclusion | Rationale | Stakeholder Impact |
|----|-----------|-----------|-------------------|
| OS-001 | Mobile Application (Native) | FRs assume web-based interaction; mobile would require separate codebases (iOS/Android), testing overhead, and ongoing operational costs. The team lacks expertise in mobile development. | **Acceptable** – Responsive web application meets 80% of user needs for M1. Management approved web-only for Phase 1. |
| OS-002 | Email/WhatsApp Integration | Not covered in FRs. Would require complex API integration, message parsing, and authentication challenges with significant security and reliability risks. | **Acceptable** – Requesters will be onboarded to the web platform. |
| OS-003 | Real-time Notifications (SMS/Push) | FRs do not require push notifications. Real-time notifications require third-party SMS gateways or push services with ongoing operational costs not approved in the budget. | **Acceptable** – Feedback provided via web interface and email updates only. |
| OS-004 | Advanced Analytics / BI Reports | FR-007 only requires basic operational metrics. Complex reporting and data visualisation tools are outside current scope. | **Acceptable** – Management accepts basic dashboard for M1, with advanced reporting deferred. |
| OS-005 | Multi-language Support | Not identified as a stakeholder need in stakeholder analysis. Multi-language support would add significant UI, validation, and error messaging complexity without immediate stakeholder value. | **Acceptable** – Staff confirmed English-only is acceptable for initial deployment. |

---

## 3. Deferred / Future Scope

| ID | Deferred Item | Reason for Deferral |
|----|---------------|---------------------|
| DS-001 | Mobile Application | Requires separate platform development and user research. |
| DS-002 | Email Integration (Inbound) | Allows time to evaluate email parsing libraries and security implications. |
| DS-003 | SLA (Service Level Agreement) Reporting | Requires operational data to define meaningful SLAs. |
| DS-004 | Automated Escalation Rules | Depends on actual usage patterns and staff workflows observed post-deployment. |
| DS-005 | Bulk Request Import (CSV/Excel) | Staff capability to batch-create requests for maintenance events. |

---
