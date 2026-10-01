# Staff Assignment Module

> **Capability:** Assign, accept, comment, resolve requests
> **ASRs:** FR-004/005 (RBAC, state machine), NFR-004

---

## Layer Overview

| # | Layer | Primary Responsibility |
|---|-------|------------------------|
| 1 | 🎨 Presentation | HTTP endpoints for assignment, comments, and resolution |
| 2 | ⚙️ Application | Orchestrates assignment, acceptance, commenting, resolution |
| 3 | 🧠 Domain | Assignment rules, entities, and policy enforcement |
| 4 | 🔧 Infrastructure | Repositories and cross-module clients |
| 5 | 🗄️ Data Persistence | Tables for assignments, comments, and actions |

---

## 🎨 Presentation Layer

| Component | Type | Details |
|-----------|------|---------|
| `AssignmentController` | Controller | `POST /api/requests/{id}/assign` |
| `CommentController` | Controller | `POST /api/requests/{id}/comments` |
| `ResolutionController` | Controller | `POST /api/requests/{id}/resolve` |
| `AssignRequestDTO` | DTO | Inbound payload for assigning a request |
| `CommentDTO` | DTO | Inbound/outbound comment representation |
| `ResolutionDTO` | DTO | Inbound payload for resolving a request |

---

## ⚙️ Application Layer

| Use Case | Requirement | Notes |
|----------|-------------|-------|
| `AssignRequestUseCase` | FR-004 | Assigns a request to a STAFF member |
| `AcceptRequestUseCase` | FR-004 | Staff accepts an assignment |
| `AddCommentUseCase` | FR-006 | Adds a comment to a request |
| `ResolveRequestUseCase` | FR-005, FR-006 | Annotated with `@Transactional` |

---

## 🧠 Domain Layer

| Component | Type | Attributes / Notes |
|-----------|------|--------------------|
| `Assignment` | Entity | `requestId`, `assignedTo`, `assignedBy`, `assignedAt`, `status` |
| `Comment` | Entity | `requestId`, `authorId`, `content`, `createdAt` |
| `Action` | Entity | `requestId`, `userId`, `actionType`, `details`, `timestamp` |
| `AssignmentPolicy` | Domain Service | Only STAFF can be assigned; only MANAGEMENT can reassign |

---

## 🔧 Infrastructure Layer

| Component | Implements / Uses | Purpose |
|-----------|-------------------|---------|
| `AssignmentRepositoryImpl` | `AssignmentRepository` | Persistence for `Assignment` entity |
| `CommentRepositoryImpl` | `CommentRepository` | Persistence for `Comment` entity |
| `ActionRepositoryImpl` | `ActionRepository` | Persistence for `Action` entity |
| `RequestModuleClient` | — | In-process call to Request Management module |
| `IdentityModuleClient` | — | In-process call to Identity & Access module |

---

## 🗄️ Data Persistence Layer

| Table | Columns |
|-------|---------|
| `assignments` | `id`, `request_id`, `assigned_to`, `assigned_by`, `assigned_at`, `status` |
| `comments` | `id`, `request_id`, `author_id`, `content`, `created_at` |
| `actions` | `id`, `request_id`, `user_id`, `action_type`, `details`, `timestamp` |

---

## Layer Dependencies

| From | → To | Direction |
|------|------|-----------|
| Presentation | Application | ↓ |
| Application | Domain | ↓ |
| Domain | Infrastructure | ↑ (via interfaces) |
| Infrastructure | Data Persistence | ↓ |

---

## Requirement Traceability

| Requirement | Implemented By |
|-------------|----------------|
| FR-004 | `AssignRequestUseCase`, `AcceptRequestUseCase`, `AssignmentPolicy` |
| FR-005 | `ResolveRequestUseCase` (state machine integration) |
| FR-006 | `AddCommentUseCase`, `ResolveRequestUseCase` |
| NFR-004 | `Action` entity, `ActionRepositoryImpl` |
