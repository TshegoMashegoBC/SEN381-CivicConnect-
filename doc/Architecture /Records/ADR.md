# Architecture Decision Records (ADRs)

> A collection of architectural decisions for the Service Request Management System.

---

## ADR-A01 — Architectural Style

| Field | Content |
|-------|---------|
| **Context** | Architectural Style Decision |
| **Constraints** | CR-001 (free tier), CR-002 (3 students), SH-001 (4 milestones), NFR-004 (audit atomicity), NFR-001 (performance) |
| **Alternatives** | A: Unstructured Monolith; B: Modular Monolith; C: Microservices; D: SOA; E: Serverless |
| **Decision** | **Modular Monolith** |
| **Rationale** | Single deployable enables ACID transactions for NFR-004; module boundaries provide maintainability without distributed complexity; fits free-tier hosting and 3-person team capacity |
| **Trade-offs** | Limited selective module scaling; single point of failure; all modules share one runtime |
| **Risks** | R-001 (skills gap), R-003 (free-tier limits) |
| **Evidence** | M2 Brief §5.3; Master Brief §18.1; Selected Architecture doc |
| **Later consequence** | To be updated at M3/M4 once implementation evidence exists. |

---

## ADR-A02 — Module Boundaries

| Field | Content |
|-------|---------|
| **Context** | Defining module boundaries from business capabilities |
| **Constraints** | SC-001 (fixed scope), FR-001–FR-007, SE-004 (RBAC), NFR-004 (audit) |
| **Alternatives** | A: 3 modules (Request, Staff, Admin); B: 5 modules (Identity, Request, Staff, Reporting, Audit); C: 7 modules (one per FR) |
| **Decision** | **5 Modules:** Identity & Access, Request Management, Staff Assignment, Reporting & Oversight, Audit & Compliance |
| **Rationale** | Each module owns distinct data and has clear responsibility; Audit and Identity are genuinely cross-cutting; 5 modules fit team capacity |
| **Trade-offs** | More modules = more interfaces to maintain; some overlap between Request and Staff |
| **Risks** | Interface drift if not governed |
| **Evidence** | M2 Brief §5.4; Selected Architecture doc |
| **Later consequence** | To be updated at M3/M4 once implementation evidence exists. |

---

## ADR-A03 — Layering Strategy

| Field | Content |
|-------|---------|
| **Context** | Internal structure per module; testability required |
| **Constraints** | NFR-004 (audit atomicity), QU-003 (test evidence), NFR-001 (performance) |
| **Alternatives** | A: 3 layers (Controller, Service, Repository); B: 5 layers (Presentation, Application, Domain, Infrastructure, Data); C: Hexagonal/Ports & Adapters |
| **Decision** | **5-Layer Architecture per Module** |
| **Rationale** | Domain layer pure and testable; Application layer owns transactions for NFR-004; Infrastructure swappable; Data isolated |
| **Trade-offs** | More files/classes; potential over-engineering for simple CRUD |
| **Risks** | Boilerplate fatigue; layer leakage |
| **Evidence** | M2 Brief §5.6; Master Brief §15 |
| **Later consequence** | To be updated at M3/M4 once implementation evidence exists. |

---

## ADR-A04 — Inter-Module Communication

| Field | Content |
|-------|---------|
| **Context** | How modules collaborate without coupling |
| **Constraints** | NFR-004 (atomicity), NFR-001 (performance), CR-002 (team size) |
| **Alternatives** | A: In-process method calls; B: Domain events; C: HTTP between modules; D: Message queue |
| **Decision** | **In-process method calls** for synchronous; **in-process domain events** for audit |
| **Rationale** | Keeps NFR-004 transaction boundary intact; no network latency; simpler for 3-person team; domain events decouple audit without leaving process |
| **Trade-offs** | Tight coupling at compile time; cannot scale modules independently; event ordering complexity |
| **Risks** | Event loss if not transactional; circular dependencies |
| **Evidence** | M2 Brief §5.7; Selected Architecture doc |
| **Later consequence** | To be updated at M3/M4 once implementation evidence exists. |

---

## ADR-A05 — Data Ownership

| Field | Content |
|-------|---------|
| **Context** | Ensuring data integrity and preventing hidden coupling |
| **Constraints** | NFR-004 (atomicity), NFR-002 (concurrency), QU-003 (traceability) |
| **Alternatives** | A: Shared tables across modules; B: One schema, one owner per table; C: Database per module |
| **Decision** | **One schema, one owner per table** |
| **Rationale** | Single database enables ACID for NFR-004; table ownership prevents hidden coupling; simpler than multiple databases |
| **Trade-offs** | Cannot enforce at DB level easily; cross-module queries possible |
| **Risks** | Accidental cross-module joins; schema coupling |
| **Evidence** | M2 Brief §5.4; Master Brief §11 |
| **Later consequence** | To be updated at M3/M4 once implementation evidence exists. |

---

## ADR-A06 — Audit Implementation

| Field | Content |
|-------|---------|
| **Context** | NFR-004 requires atomic audit logging |
| **Constraints** | NFR-004 (audit logging), SE-005 (residual risk) |
| **Alternatives** | A: DB triggers; B: ORM hooks; C: Service-layer explicit; D: Dedicated Audit module called in same transaction |
| **Decision** | **Dedicated Audit module called within the same transaction** |
| **Rationale** | Explicit, testable, framework-agnostic; same transaction guarantees atomicity (NFR-004); module owns audit schema |
| **Trade-offs** | Requires discipline to call Audit module; potential to forget |
| **Risks** | Missing audit entries if call omitted; performance overhead |
| **Evidence** | M1 PED NFR-004; Master Brief §15 |
| **Later consequence** | To be updated at M3/M4 once implementation evidence exists. |

---

## ADR-A07 — API Style

| Field | Content |
|-------|---------|
| **Context** | SPA-to-backend communication with RBAC |
| **Constraints** | SE-004 (RBAC), NFR-003 (TLS), NFR-001 (performance) |
| **Alternatives** | A: REST + JWT; B: GraphQL; C: SOAP; D: gRPC |
| **Decision** | **REST + JWT** |
| **Rationale** | Simple, well-understood; JWT carries role claims for RBAC; works with free-tier hosting; browser-friendly |
| **Trade-offs** | Over-fetching/under-fetching; no built-in schema validation |
| **Risks** | Token expiry handling; JWT revocation |
| **Evidence** | Master Brief §9; Selected Architecture doc |
| **Later consequence** | To be updated at M3/M4 once implementation evidence exists. |

---

## ADR-A08 — Deployment Topology

| Field | Content |
|-------|---------|
| **Context** | Free-tier constraint; single deployable |
| **Constraints** | CR-001 (free tier), NFR-003 (SSL), NFR-005 (availability) |
| **Alternatives** | A: Single container (API + SPA); B: Separate API + static SPA; C: Serverless functions |
| **Decision** | **Separate API + static SPA** |
| **Rationale** | SPA on CDN (free); API on Render/Fly free tier; simpler CI/CD; SSL handled by platform |
| **Trade-offs** | Two deploy targets; CORS configuration; environment sync |
| **Risks** | Free-tier sleep (NFR-005); cold starts (NFR-001) |
| **Evidence** | M2 Brief §5.8; Master Brief §17 |
| **Later consequence** | To be updated at M3/M4 once implementation evidence exists. |

---

## ADR-A09 — Status Transition Pattern

| Field | Content |
|-------|---------|
| **Context** | FR-005 requires controlled transitions; NFR-004 requires audit |
| **Constraints** | FR-005, NFR-004, SE-004 (RBAC) |
| **Alternatives** | A: Enum + if/else; B: State Pattern (GoF); C: Table-driven state machine; D: Workflow engine (e.g., Camunda) |
| **Decision** | **Table-driven state machine** |
| **Rationale** | Data-driven, easy to audit, easy to extend; no class explosion; RBAC integrated into transition table |
| **Trade-offs** | Less type-safe than State Pattern; runtime errors if table malformed |
| **Risks** | Invalid transitions if table not validated; missing role checks |
| **Evidence** | M2 Brief §5.6; M1 PED FR-005 |
| **Later consequence** | To be updated at M3/M4 once implementation evidence exists. |

---

## ADR-A10 — Reporting Implementation

| Field | Content |
|-------|---------|
| **Context** | FR-007 requires aggregate views; NFR-002 (concurrency) |
| **Constraints** | FR-007, NFR-002 (concurrency), NFR-001 (performance) |
| **Alternatives** | A: Reporting owns its own tables (CQRS); B: Reporting queries other modules' tables read-only; C: Reporting calls other modules' APIs |
| **Decision** | **Read-only queries against module tables** |
| **Rationale** | No data duplication; simpler for MVP; read replicas not needed at this scale |
| **Trade-offs** | Coupling to schema; performance impact on write tables; no independent scaling |
| **Risks** | Schema changes break reporting; query performance |
| **Evidence** | M2 Brief §5.4; Master Brief §15 |
| **Later consequence** | To be updated at M3/M4 once implementation evidence exists. |

---

## Summary

| ADR | Title | Decision |
|-----|-------|----------|
| A01 | Architectural Style | Modular Monolith |
| A02 | Module Boundaries | 5 Modules |
| A03 | Layering Strategy | 5-Layer Architecture per Module |
| A04 | Inter-Module Communication | In-process calls + domain events |
| A05 | Data Ownership | One schema, one owner per table |
| A06 | Audit Implementation | Dedicated Audit module in same transaction |
| A07 | API Style | REST + JWT |
| A08 | Deployment Topology | Separate API + static SPA |
| A09 | Status Transition Pattern | Table-driven state machine |
| A10 | Reporting Implementation | Read-only queries against module tables |
