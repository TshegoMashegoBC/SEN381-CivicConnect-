# Constraints Analysis

## 1. Scope Constraints

| ID | Constraint | Description | Engineering Implication |
|----|------------|-------------|------------------------|
| SC-001 | Fixed Scope | All 7 FRs (FR-001 to FR-007) identified are committed to the baseline. | Cannot drop any FRs; any scope change requires formal change control with impact analysis. |
| SC-002 | Minimum Capabilities | The system must support all Minimum Business Capabilities defined in the Master Project Brief. | Cannot drop core features; prioritisation applies to additional features only. |
| SC-003 | No Unapproved Additions | Additional features are permitted only if justified by stakeholder value and effect on scope/schedule/quality/security/risk is considered. | Each proposed new FR requires impact analysis; "nice-to-have" features are deferred to DS-001 to DS-005. |

---

## 2. Schedule Constraints

| ID | Constraint | Description | Engineering Implication |
|----|------------|-------------|------------------------|
| SH-001 | 4 Milestones | Four formal milestones within the SEN381 delivery period: M1 (Week 4), M2 (Week 8), M3 (Week 12), M4 (Week 16). | Cannot delay M1 without affecting downstream phases. All 7 FRs and 6 NFRs must be deliverable within the fixed timeline. |
| SH-002 | Fixed End Date | No extension beyond academic calendar. | Scope must be realistic; defer non-essential features (DS-001 to DS-005) to maintain quality. |
| SH-003 | Review Lead Time | Two-reviewer approval required for substantive changes entering main. | Team must plan for review cycles; cannot rush changes at the last minute. |

---

## 3. Cost / Resource Constraints

| ID | Constraint | Description | Engineering Implication |
|----|------------|-------------|------------------------|
| CR-001 | Free/Low-Cost Services | Prefer free or low-cost services where practical; identify limitations. | Technology choices must evaluate free-tier limitations like deployment platform free tier, database size limits, connection limits. Must support all 7 FRs and 6 NFRs on free tier. |
| CR-002 | Team Size | Exactly 3 students; no additional paid developers. | Work must be divided efficiently; complexity must match team capacity. |
| CR-003 | Operational Cost | Likely operational cost beyond educational context must be identified. | Hosting, domain, third-party API costs must be documented and reasonable. |
| CR-004 | Learning Curve | Unfamiliar technologies introduce schedule risk. | Technology selection must consider team's existing skills; if adopting new tech, factor in learning time. |

---

## 4. Quality Constraints

| ID | Constraint | Description | Engineering Implication |
|----|------------|-------------|------------------------|
| QU-001 | Measurable Quality | Quality attributes must be defined and supported by measurable evidence. | All NFRs (NFR-001 to NFR-006) are written with measurable targets. |
| QU-002 | Acceptance Criteria | Important requirements must have acceptance criteria. | Every FR and every NFR has defined acceptance criteria. |
| QU-003 | Test Evidence | Test coverage alone is insufficient; evidence must show what was verified and what was not. | Unit tests, integration tests, and manual tests must be traceable to requirements in the RTM. |
| QU-004 | Defect Management | Defects must be recorded with severity/priority and disposition. | Cannot ignore bugs; must track and address them. |

---

## 5. Security Constraints

| ID | Constraint | Description | Engineering Implication |
|----|------------|-------------|------------------------|
| SE-001 | Lifecycle Responsibility | Security is a lifecycle-wide responsibility, not a final add-on. | NFR-003 (encryption) and NFR-004 (audit logging) defined in M1; security must be considered in M2 architecture, M3 implementation, and M4 deployment. |
| SE-002 | Secrets Handling | Passwords, API keys, tokens, private keys must not be committed to GitHub. | Must use environment variables and `.gitignore`; enforce with GitHub pre-commit hooks. |
| SE-003 | Data in Transit | All sensitive data (requests, user info) must be encrypted in transit (TLS 1.2+). | Technology choice must support HTTPS; deployment platform must provide SSL certificates (NFR-003). |
| SE-004 | Authentication & RBAC | Authentication and Role-Based Access Control are mandatory. | Cannot skip authentication for simplicity; must design RBAC from the start (supports FR-003, FR-004, FR-007). |
| SE-005 | Residual Risks | Cannot claim system is "secure"; record residual security risks. | Threat modelling must identify weaknesses; risk register must document accepted risks. |

---

## 6. Constraint Trade-Off Analysis

### Cost vs. Scalability

| Dimension | Detail |
|-----------|--------|
| **The Trade-Off** | Zero/Low-cost hosting (CR-001) vs. future scalability requirements (NFR-002: 50 concurrent users) |
| **Constraint A** | Cost: Prefer free or low-cost services (CR-001) |
| **Constraint B** | Quality: System must handle at least 50 concurrent users (NFR-002) |
| **Analysis** | Free-tier cloud services typically have limitations: CPU throttling, database connection limits (often 20 connections), storage caps, and cold-start delays. 50 concurrent users may exceed free-tier limits. |
| **Decision** | Select a platform that provides a generous free tier with documented scalability options. Design the application to be stateless so it can scale horizontally when needed. Use connection pooling to manage database connections efficiently. |
| **Ripple Effect** | The architecture must separate the web application from the database so they can be scaled independently. This may influence M2 architecture decisions. |
| **Risk** | If the organisation grows beyond the free tier capacity, operational costs will increase. The team must document the projected cost per 1,000 users and include this in the final project evaluation. |

---

## Summary

| Category | Count | Key Focus Areas |
|----------|-------|-----------------|
| **Scope Constraints** | 3 | Fixed scope, minimum capabilities, no unapproved additions |
| **Schedule Constraints** | 3 | 4 milestones, fixed end date, review lead time |
| **Cost/Resource Constraints** | 4 | Free services, team size, operational cost, learning curve |
| **Quality Constraints** | 4 | Measurable quality, acceptance criteria, test evidence, defect management |
| **Security Constraints** | 5 | Lifecycle responsibility, secrets, encryption, RBAC, residual risks |
| **Trade-Offs** | 1 | Cost vs. Scalability |

---
