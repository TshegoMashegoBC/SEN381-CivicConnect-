# Identity & Access Module

> **Capability:** Authentication, Authorisation, Role Management
> **ASRs:** SE-004 (RBAC), NFR-003 (TLS)

---

## Layer Overview

| # | Layer | Primary Responsibility |
|---|-------|------------------------|
| 1 | 🎨 Presentation | HTTP endpoints, request validation, DTO mapping |
| 2 | ⚙️ Application | Orchestrates use cases and domain logic |
| 3 | 🧠 Domain | Business rules, entities, value objects, services |
| 4 | 🔧 Infrastructure | Technical implementations (JWT, BCrypt, repos) |
| 5 | 🗄️ Data Persistence | Database schema and migrations |

---

## 🎨 Presentation Layer

| Component | Type | Details |
|-----------|------|---------|
| `AuthController` | Controller | `POST /api/auth/login`, `POST /api/auth/register`, `POST /api/auth/refresh` |
| `UserController` | Controller | `GET /api/users/me`, `GET /api/users/{id}` |
| `JwtAuthMiddleware` | Middleware | JWT token extraction & validation |
| `LoginRequest` | DTO | Inbound login payload |
| `AuthResponse` | DTO | Outbound token + user info |
| `UserDTO` | DTO | Outbound user representation |

---

## ⚙️ Application Layer

| Use Case | Responsibility |
|----------|----------------|
| `LoginUseCase` | Authenticate credentials and issue JWT |
| `RegisterUserUseCase` | Create user with assigned role |
| `AuthoriseUseCase` | Check permission for a given action |
| `RefreshTokenUseCase` | Issue new access token from refresh token |

---

## 🧠 Domain Layer

| Component | Type | Attributes / Notes |
|-----------|------|--------------------|
| `User` | Entity | `id`, `email`, `passwordHash`, `role`, `status` |
| `Role` | Enum | `REQUESTER`, `STAFF`, `MANAGEMENT` |
| `Permission` | Value Object | `action`, `resource` |
| `PasswordPolicy` | Domain Service | Enforces password strength rules |
| `RolePermissionMatrix` | Domain Service | Maps roles to permitted actions |

---

## 🔧 Infrastructure Layer

| Component | Implements / Uses | Purpose |
|-----------|-------------------|---------|
| `UserRepositoryImpl` | `UserRepository` | Persistence for `User` entity |
| `PasswordEncoder` | BCrypt | Hash & verify passwords |
| `JwtTokenProvider` | — | Sign, verify, and refresh JWT tokens |
| `EmailServiceClient` | — | Password reset (deferred) |

---

## 🗄️ Data Persistence Layer

| Table | Columns |
|-------|---------|
| `users` | `id`, `email`, `password_hash`, `role`, `created_at` |
| `sessions` | `id`, `user_id`, `token_hash`, `expires_at` |

| Migration | Description |
|-----------|-------------|
| `V1__create_users.sql` | Creates `users` table |
| `V2__create_sessions.sql` | Creates `sessions` table |

---

## Layer Dependencies

| From | → To | Direction |
|------|------|-----------|
| Presentation | Application | ↓ |
| Application | Domain | ↓ |
| Domain | Infrastructure | ↑ (via interfaces) |
| Infrastructure | Data Persistence | ↓ |

> **Note:** Domain sits at the core and depends on no outer layer. Infrastructure implements interfaces defined by the Domain (Dependency Inversion Principle).
