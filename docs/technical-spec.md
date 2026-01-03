## Technical Specification: Multi-Tenant SaaS Platform

**Project Name:** Multi-Tenant SaaS Project Management System

**Date:** October 26, 2025

**Version:** 1.0

**Status:** Implementation Ready

---

## 1. System Architecture Overview

The platform implements a **containerized monorepo** strategy. By encapsulating the Frontend, Backend, and Database into a single orchestrated unit, we ensure environment consistency and simplified dependency management.

---

## 2. Codebase Organization

### 2.1 Root Level Orchestration

The root directory manages the global configuration and service discovery.

```text
/Multi-Tenant-SaaS-Platform
├── docker-compose.yml       # Service orchestration & networking
├── submission.json          # Evaluation metadata
├── docs/                    # Technical documentation & ERDs
├── backend/                 # Node.js/Express API service
└── frontend/                # React/Vite client service

```

### 2.2 Backend Service Implementation (`/backend`)

The API follows a **Modular Layered Architecture**, separating routing, business logic, and data persistence.

* **ORM:** Prisma (PostgreSQL adapter).
* **Isolation:** Middleware-level tenant context injection.

```text
backend/
├── prisma/                  # Schema modeling & migrations
├── src/
│   ├── controllers/         # Request handling & Response formatting
│   ├── middleware/          # JWT validation & Tenant-ID enforcement
│   ├── routes/              # Express router definitions
│   └── utils/               # Cryptography & JWT generators

```

### 2.3 Frontend Service Implementation (`/frontend`)

The client is a **Stateless React SPA** optimized with Vite for modern browser performance.

```text
frontend/
├── src/
│   ├── context/             # Global Auth & Tenant state providers
│   ├── pages/               # Routed view components
│   ├── components/          # Reusable UI primitives
│   └── services/            # Axios interceptors for API calls

```

---

## 3. Infrastructure & Deployment

### 3.1 Container Specifications

| Service | Image Base | Role |
| --- | --- | --- |
| **Database** | `postgres:15-alpine` | Relational storage & ACID compliance |
| **API** | `node:18-alpine` | Business logic & Identity management |
| **Client** | `node:18-alpine` | UI rendering & state management |

### 3.2 Service Networking

All services communicate over an internal Docker bridge network.

* **Backend to DB:** Authenticated via `DATABASE_URL`.
* **Frontend to Backend:** Mediated via a configurable `VITE_API_URL` environment variable.

---

## 4. Local Development Workflow

### 4.1 Prerequisites

* **Runtime:** Docker Desktop 4.0+ (required for orchestration).
* **Package Manager:** NPM 9+ (for local IntelliSense and linting).

### 4.2 Initialization Sequence

1. **Clone & Enter:**
```bash
git clone <repo_url> && cd saas-platform

```


2. **Environment Configuration:**
Ensure `backend/.env` contains valid secrets for `JWT_SECRET` and `DATABASE_URL`.
3. **Orchestration:**
```bash
docker-compose up -d --build

```


*Note: On startup, the API container automatically executes `prisma migrate deploy` and `seed.js` to prepare the environment.*

---

## 5. Quality Assurance & Validation

### 5.1 Health Monitoring

Verify the operational status of the service cluster:

```bash
curl http://localhost:5000/api/health

```

### 5.2 Persistence Validation

To audit the logical isolation of data at the database level:

```bash
docker exec -it database psql -U postgres -d saas_db -c "SELECT * FROM tenants;"

```

### 5.3 Security Hardening

* **JWT Integrity:** HS256 algorithm with a minimum 32-character secret.
* **CORS Policy:** Restricted to `FRONTEND_URL` in production environments.
* **Input Sanitization:** Enforced via Prisma's parameterized queries to prevent SQLi.

---
