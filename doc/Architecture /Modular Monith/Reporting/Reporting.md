# Reporting & Oversight Module

> **Capability:** Monitor open/overdue/resolved/closed requests
> **ASRs:** NFR-002 (concurrency), NFR-005 (availability)

---

## Layer Overview

| # | Layer | Primary Responsibility |
|---|-------|------------------------|
| 1 | 🎨 Presentation | Dashboard and report HTTP endpoints |
| 2 | ⚙️ Application | Reporting use cases and aggregation orchestration |
| 3 | 🧠 Domain | Report specifications, read models, overdue policy |
| 4 | 🔧 Infrastructure | Read-only query repos, caching, module clients |
| 5 | 🗄️ Data Persistence | Read-only views & materialized views (no owned tables) |

---

## 🎨 Presentation Layer

| Component | Type | Details |
|-----------|------|---------|
| `DashboardController` | Controller | `GET /api/reports/dashboard` |
| `ReportController` | Controller | `GET /api/reports/requests?groupBy=status` |
| `DashboardDTO` | DTO | Outbound dashboard snapshot |
| `StatusSummaryDTO` | DTO | Outbound status count / percentage |
| `CategoryBreakdownDTO` | DTO | Outbound category-level breakdown |

---

## ⚙️ Application Layer

| Use Case | Requirement | Notes |
|----------|-------------|-------|
| `GetDashboardUseCase` | FR-007 | Aggregates KPIs for the dashboard |
| `GetStatusSummaryUseCase` | FR-007 | Counts requests grouped by status |
| `GetCategoryBreakdownUseCase` | FR-007 | Counts requests grouped by category |
| `GetOverdueRequestsUseCase` | FR-007 | Lists requests past the overdue threshold |

---

## 🧠 Domain Layer

| Component | Type | Attributes / Notes |
|-----------|------|--------------------|
| `ReportSpecification` | Value Object | `filters`, `groupBy`, `dateRange` |
| `StatusSummary` | Read Model | `status`, `count`, `percentage` |
| `OverduePolicy` | Domain Service | Defines the overdue threshold |

---

## 🔧 Infrastructure Layer

| Component | Implements / Uses | Purpose |
|-----------|-------------------|---------|
| `ReportQueryRepositoryImpl` | `ReportQueryRepository` | Read-only, optimized queries |
| `CacheService` | Redis / in-memory | Caches dashboard data (NFR-001) |
| `RequestModuleClient` | — | In-process call for read models |

---

## 🗄️ Data Persistence Layer

| Artifact | Type | Notes |
|----------|------|-------|
| `requests`, `assignments` | Source tables (read-only) | Queried via read-only views / queries |
| `request_status_summary` | Materialized View | Refreshed periodically |
| — | — | **No owned tables** — reads from other modules' data |

---

## Layer Dependencies

| From | → To | Direction |
|------|------|-----------|
| Presentation | Application | ↓ |
| Application | Domain | ↓ |
| Domain | Infrastructure | ↑ (via interfaces) |
| Infrastructure | Data Persistence | ↓ |

> **Note:** This module owns **no tables**. It reads from other modules' data via read-only views and a materialized view (`request_status_summary`). The `CacheService` reduces load on the underlying stores, supporting NFR-002 (concurrency) and NFR-005 (availability).

---

## Requirement Traceability

| Requirement | Implemented By |
|-------------|----------------|
| FR-007 | `GetDashboardUseCase`, `GetStatusSummaryUseCase`, `GetCategoryBreakdownUseCase`, `GetOverdueRequestsUseCase` |
| NFR-001 | `CacheService` (dashboard caching) |
| NFR-002 | Read-only queries + materialized view (concurrency) |
| NFR-005 | `CacheService`, read-only views (availability) |
