## 6.1 Key Entities

CivicConnect's functional requirements (FR-001–FR-007) and the audit-logging non-functional requirement (NFR-004) point to five core entities. Ownership and lifecycle are stated because NFR-004 requires that every status change be atomically and permanently recorded — this shapes how StatusHistory relates to ServiceRequest below.

| Entity | Purpose / key attributes | Owns / owned by | Lifecycle note |
|---|---|---|---|
| User | Requester, Staff or Management account; role, contact details, authentication credential (hashed) | Owns zero or more ServiceRequest records as requester; owned by no other entity | Created at registration; role is fixed by an administrator, not self-selected (supports SE-004/ASR-003 RBAC) |
| ServiceRequest | Core record: description, category, requester, assigned staff member, current status, created/updated timestamps | Owns its StatusHistory entries and Comment entries; references Category and two User roles (requester, assignee) | Created on submission (FR-001); status changes only through the controlled transitions in Design Problem 1 (Section 8.1); never hard-deleted, only closed, to preserve the audit trail |
| StatusHistory | One row per status change: previous status, new status, changed-by user, timestamp, optional comment | Owned by exactly one ServiceRequest; immutable once written | Insert-only. This is the entity that directly satisfies NFR-004 and must be written in the same transaction as the status update it records (see 6.3) |
| Comment | Free-text note or resolution detail attached to a request | Owned by exactly one ServiceRequest; authored by one User | Created by staff (FR-006); not editable after creation, to preserve accountability for what was recorded and when |
| Category | Controlled list of request types (e.g. facility fault, IT support, lost property) | Referenced by many ServiceRequest records; independent lifecycle, managed by Management/Staff | Seeded at setup; adding a category is a controlled, low-frequency change, not a per-request free-text field (supports FR-001's "controlled category mechanism") |
