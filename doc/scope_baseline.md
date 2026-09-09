# Scope Baseline

## 1. In-Scope

All FRs identified are committed to the M1 baseline:

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

## 4. Deliberate Exclusion Defence: OS-001 (Mobile Application)

### Rationale

FRs (FR-001 to FR-007) all assume web-based interaction. Building a native mobile application would introduce:

1. **Development Complexity:** Requires separate codebases (iOS/Android) or cross-platform frameworks (React Native/Flutter), which the team does not currently have expertise in.

2. **Testing Overhead:** Mobile testing requires device fragmentation, OS version compatibility, and App Store/Play Store submission processes.

3. **Operational Cost:** Ongoing maintenance, updates, developer accounts ($99/year for Apple), and store fees.

4. **Security Risk:** Mobile device security, data storage on devices, and offline capabilities introduce additional threat vectors not covered by the security requirements.

### Stakeholder Agreement

Management agreed that a responsive web application (accessible via mobile browser) will meet 80% of user needs for M1, with a dedicated mobile application evaluated after Phase 1 based on actual usage data.

### Downstream Consequence

By excluding the mobile app now, we preserve development capacity for core web functionality. However, this forces the UI design to be fully responsive and touch-friendly to accommodate mobile browser users. This constraint will influence M2 design decisions (e.g., choosing a responsive CSS framework such as Bootstrap or Tailwind).

---

## Summary

| Category | Count |
|----------|-------|
| **In-Scope Items** | 7 |
| **Out-of-Scope Exclusions** | 5 |
| **Deferred Items** | 5 |

---
