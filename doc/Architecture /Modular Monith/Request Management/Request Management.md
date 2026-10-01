# Request Management Module

> **Capability:** Submit, view, track service requests
> **ASRs:** NFR-004 (audit), NFR-001 (performance)

---

## Layer Overview

| # | Layer | Primary Responsibility |
|---|-------|------------------------|
| 1 | 🎨 Presentation | HTTP endpoints, request validation, DTO mapping |
| 2 | ⚙️ Application | Orchestrates use cases and domain logic |
| 3 | 🧠 Domain | Business rules, aggregates, entities, services |
| 4 | 🔧 Infrastructure | Repositories, audit integration, technical adapters |
| 5 | 🗄️ Data Persistence | Database schema, indexes, and migrations |

---

## 🎨 Presentation Layer

| Component | Type | Details |
|-----------|------|---------|
| `RequestController` | Controller | `POST /api/requests`, `GET /api/requests/{id}` |
| `RequestListController` | Controller | `GET /api/requests?status=&category=` |
| `CreateRequestDTO` | DTO | Inbound payload for submitting a request |
| `RequestDTO` | DTO | Outbound full request representation |
| `RequestSummaryDTO` | DTO | Outbound list-view summary |

---

## ⚙️ Application Layer

| Use Case | Requirement | Notes |
|----------|-------------|-------|
| `SubmitRequestUseCase` | FR-001 | Creates a new service request |
| `ViewRequestStatusUseCase` | FR-002 | Retrieves current status of a request |
| `UpdateRequestStatusUseCase` | FR-005 | Annotated with `@Transactional` |
| `ListRequestsUseCase` | FR-003 | Supports filtering by status and category |

---

## 🧠 Domain Layer

| Component | Type | Attributes / Notes |
|-----------|------|--------------------|
| `Request` | Aggregate Root | `id`, `title`, `description`, `status`, `category`, `requesterId`, `assignedTo`, `createdAt`, `updatedAt` |
| `Status` | Enum | `SUBMITTED`, `ASSIGNED`, `IN_PROGRESS`, `RESOLVED`, `CLOSED` |
| `Category` | Entity | `id`, `name`, `description` |
| `StatusTransition` | Value Object | `from`, `to`, `allowedRoles` |
| `RequestStateMachine` | Domain Service | Enforces valid transitions (FR-005) |
| `StatusHistory` | Entity | `requestId`, `from`, `to`, `userId`, `timestamp` |

---

## 🔧 Infrastructure Layer

| Component | Implements / Uses | Purpose |
|-----------|-------------------|---------|
| `RequestRepositoryImpl` | `RequestRepository` | Persistence for `Request` aggregate |
| `StatusHistoryRepositoryImpl` | `StatusHistoryRepository` | Persists status transition records |
| `CategoryRepositoryImpl` | `CategoryRepository` | Persists and retrieves categories |
| `AuditModuleClient` | — | In-process call to Audit module (NFR-004) |

---

## 🗄️ Data Persistence Layer

### Tables

| Table | Columns |
|-------|---------|
| `requests` | `id`, `title`, `description`, `status`, `category_id`, `requester_id`, `assigned_to`, `created_at`, `updated_at` |
| `status_history` | `id`, `request_id`, `from_status`, `to_status`, `user_id`, `timestamp` |
| `categories` | `id`, `name`, `description` |

### Indexes

| Index | Table | Column(s) | Rationale |
|-------|-------|-----------|-----------|
| `idx_requests_status` | `requests` | `status` | NFR-001 (performance) |
| `idx_requests_assigned_to` | `requests` | `assigned_to` | NFR-001 (performance) |
| `idx_status_history_request_id` | `status_history` | `request_id` | NFR-002 (performance) |

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
| FR-001 | `SubmitRequestUseCase` |
| FR-002 | `ViewRequestStatusUseCase` |
| FR-003 | `ListRequestsUseCase` |
| FR-005 | `UpdateRequestStatusUseCase`, `RequestStateMachine` |
| NFR-001 | Indexes on `requests.status`, `requests.assigned_to` |
| NFR-002 | Index on `status_history.request_id` |
| NFR-004 | `AuditModuleClient`, `StatusHistory` entity |
