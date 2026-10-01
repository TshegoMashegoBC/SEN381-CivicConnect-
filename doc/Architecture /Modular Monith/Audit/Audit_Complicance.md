# Audit & Compliance Module

> **Capability:** Immutable log of all status changes
> **ASRs:** NFR-004 (audit logging)

---

## Layer Overview

| # | Layer | Primary Responsibility |
|---|-------|------------------------|
| 1 | 🎨 Presentation | Audit query and search HTTP endpoints |
| 2 | ⚙️ Application | Record, query, and export audit entries |
| 3 | 🧠 Domain | Audit entity and immutability policy |
| 4 | 🔧 Infrastructure | Append-only repository and event publisher |
| 5 | 🗄️ Data Persistence | Append-only `audit_log` table with indexes |

---

## 🎨 Presentation Layer

| Component | Type | Details |
|-----------|------|---------|
| `AuditController` | Controller | `GET /api/audit/requests/{id}` |
| `AuditSearchController` | Controller | `GET /api/audit/search?userId=&date=` |
| `AuditEntryDTO` | DTO | Outbound audit entry representation |
| `AuditSearchDTO` | DTO | Inbound search criteria |

---

## ⚙️ Application Layer

| Use Case | Requirement | Notes |
|----------|-------------|-------|
| `RecordAuditEntryUseCase` | NFR-004 | Called by other modules to record events |
| `QueryAuditLogUseCase` | FR-007 | Supports management oversight |
| `ExportAuditLogUseCase` | DS-003 | Deferred |

---

## 🧠 Domain Layer

| Component | Type | Attributes / Notes |
|-----------|------|--------------------|
| `AuditEntry` | Entity | `id`, `requestId`, `userId`, `previousStatus`, `newStatus`, `comment`, `timestamp`, `ipAddress` |
| `AuditPolicy` | Domain Service | Immutable, append-only, retention rules |

---

## 🔧 Infrastructure Layer

| Component | Implements / Uses | Purpose |
|-----------|-------------------|---------|
| `AuditRepositoryImpl` | `AuditRepository` | Append-only — no update / delete operations |
| `AuditEventPublisher` | — | Publishes in-process domain events |

---

## 🗄️ Data Persistence Layer

### Table

| Table | Columns |
|-------|---------|
| `audit_log` | `id`, `request_id`, `user_id`, `previous_status`, `new_status`, `comment`, `timestamp`, `ip_address` |

### Indexes

| Index | Column(s) | Rationale |
|-------|-----------|-----------|
| `idx_audit_request_id` | `request_id` | Fast lookup by request |
| `idx_audit_user_id` | `user_id` | Fast lookup by user |
| `idx_audit_timestamp` | `timestamp` | Fast range queries by time |

### Constraints

| Constraint | Description |
|------------|-------------|
| `NO UPDATE` | Rows cannot be modified after insertion |
| `NO DELETE` | Rows cannot be removed (append-only) |

---

## Layer Dependencies

| From | → To | Direction |
|------|------|-----------|
| Presentation | Application | ↓ |
| Application | Domain | ↓ |
| Domain | Infrastructure | ↑ (via interfaces) |
| Infrastructure | Data Persistence | ↓ |

> **Note:** `AuditPolicy` enforces immutability and append-only semantics at the domain level, while the persistence layer enforces it via constraints. Other modules record events through `RecordAuditEntryUseCase` or by subscribing to `AuditEventPublisher`.

---

## Requirement Traceability

| Requirement | Implemented By |
|-------------|----------------|
| NFR-004 | `RecordAuditEntryUseCase`, `AuditEntry`, `AuditPolicy`, `audit_log` constraints |
| FR-007 | `QueryAuditLogUseCase` |
| DS-003 | `ExportAuditLogUseCase` (deferred) |
