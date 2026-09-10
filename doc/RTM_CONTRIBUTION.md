# Requirements Traceability Matrix (RTM) Contribution

## 1. NFR Traceability

| NFR ID | Source | Acceptance Criteria | Design Link (M2) | Test Link (M3) |
|--------|--------|---------------------|------------------|----------------|
| NFR-001 | Requester | AC-NFR-001: Dashboard loads in < 2s (95% of tests) | TBD | TBD |
| NFR-002 | Staff/Management | AC-NFR-002: 50 concurrent users, avg < 3s, max < 5s | TBD | TBD |
| NFR-003 | Security Constraint | AC-NFR-003: SSL Labs rating A/A+ | TBD | TBD |
| NFR-004 | Staff/Management | AC-NFR-004: All status changes logged with required fields | TBD | TBD |
| NFR-005 | Staff/Management | AC-NFR-005: 99.5% uptime during business hours | TBD | TBD |
| NFR-006 | Org Policy (implicit) | AC-NFR-006: No WCAG 2.1 Level A violations | TBD | TBD |

---

## 2. Constraint Traceability

| Constraint ID | Source | Engineering Implication | M2 Impact | M3 Impact |
|---------------|--------|------------------------|-----------|-----------|
| SC-001 | Master Project Brief | Fixed scope; change control required | Design decisions must be reversible/adaptable | Changes require impact analysis |
| SC-002 | Master Project Brief | Minimum capabilities must be delivered | Architecture must support all FRs | All FRs must be implemented |
| SC-003 | Master Project Brief | No unapproved additions | Each proposed feature requires justification | No unapproved scope creep |
| SH-001 | Master Project Brief | 4 milestones, fixed dates | Must deliver architecture by M2 | Must deliver working code by M3 |
| SH-002 | Master Project Brief | Fixed end date | Realistic scope; defer non-essential features | Must complete by deadline |
| SH-003 | Master Project Brief | 2 approvals for PRs | Plan for review cycles | Cannot rush changes |
| CR-001 | Master Project Brief | Free/low-cost services | Platform selection constrained by free-tier limits | Deployment platform must be free/compatible |
| CR-002 | Master Project Brief | Team of 3 | Complexity must match team capacity | Work must be divided efficiently |
| CR-003 | Master Project Brief | Operational cost | Document projected costs | Include cost review in final |
| CR-004 | Team Analysis | Learning curve | Factor learning time into planning | Monitor learning curve impact |
| QU-001 | Master Project Brief | NFRs must be measurable | Architecture must support performance testing | Tests must validate NFRs |
| QU-002 | Master Project Brief | Acceptance criteria | Design must meet ACs | Tests must verify ACs |
| QU-003 | Master Project Brief | Test evidence | Architecture must support testability | Tests must be traceable to requirements |
| QU-004 | Master Project Brief | Defect management | Track defects | Address defects systematically |
| SE-001 | Master Project Brief | Security lifecycle-wide | Security patterns built into architecture | Security scanning in CI/CD |
| SE-002 | Master Project Brief | Secrets handling | Environment variables in architecture | No secrets in GitHub |
| SE-003 | Master Project Brief | Data in transit encryption | Platform must support HTTPS | TLS enforced |
| SE-004 | Master Project Brief | Authentication & RBAC | Design RBAC from the start | Implement authentication |
| SE-005 | Master Project Brief | Residual risks | Document accepted risks | Record residual security risks |

---

## 3. Traceability Summary

| Category | Count | Status |
|----------|-------|--------|
| **NFRs** | 6 | Traceability mapped |
| **Constraints** | 19 | Traceability mapped |
| **Design Links** | 0 / 25 | TBD (M2) |
| **Test Links** | 0 / 25 | TBD (M3) |

---
