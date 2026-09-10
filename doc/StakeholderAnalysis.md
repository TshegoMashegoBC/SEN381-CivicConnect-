# 2. Stakeholder Analysis

Three primary stakeholder groups were identified from the CivicConnect scenario. Their needs, and their relative influence and interest in the platform, are summarised below.

| Stakeholder | Main Needs | Influence / Interest |
|---|---|---|
| **Requesters** | Submit service requests; track status; view request history; receive meaningful, timely feedback when a request is accepted, updated or completed. | Medium influence / High interest |
| **Staff** | View and search relevant requests; assign or accept responsibility; update status through controlled transitions; record actions/comments; resolve or close requests efficiently. | High influence / High interest |
| **Management** | Monitor outstanding, overdue, resolved and closed requests; view activity by category/status; access enough information to support accountability and service-performance reporting. | High influence / High interest |

## 2.1 Stakeholder Conflict / Competing Expectations

The clearest tension identified is between requester transparency and staff workload control. Requesters want frequent, granular visibility into their request (who it is assigned to, why it is delayed, what is being done), because this directly drives their trust in the service. Staff, however, need the freedom to triage, batch and prioritise work without being interrupted by constant status queries, and are wary of a system that exposes internal workload or assignment decisions in a way that invites pressure from requesters or management.

This conflict was resolved for the M1 baseline by scoping requester-facing status visibility to a small set of controlled, staff-driven status values (e.g. Submitted → Assigned → In Progress → Resolved → Closed) rather than free-text or real-time internal detail. This satisfies the requester's core need for visibility (FR-002) while preserving staff control over how and when status changes are communicated (FR-005), and is reflected as a deliberate scope decision the team can defend at the M1 baseline sign-off.
