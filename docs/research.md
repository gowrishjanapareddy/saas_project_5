## Architectural Research & Decision Document: Multi-Tenancy Strategy

**Project:** Multi-Tenant SaaS Project Management System

**Date:** October 26, 2025

**Classification:** Technical / Infrastructure

**Status:** Approved for Implementation

---

## 1. Multi-Tenancy Architecture Analysis

The primary objective of this research is to define the optimal data isolation strategy for a high-concurrency SaaS platform. Multi-tenancy involves serving multiple customer organizations (tenants) from a shared computing environment while ensuring strict data privacy.

### 1.1 Comparative Strategy Matrix

We evaluated three industry-standard models for data isolation at the database tier.

| Feature | **Shared Schema (Pool)** | **Separate Schema (Bridge)** | **Separate Database (Silo)** |
| --- | --- | --- | --- |
| **Isolation** | Logical (Row-level) | Logical (Namespace) | Physical |
| **Resource Efficiency** | Extremely High | Moderate | Low |
| **Onboarding Latency** | Near-Zero | Low | High |
| **Operational Complexity** | Low | High | Extremely High |
| **Data Leak Risk** | Managed via Middleware | Lower | Minimal |

### 1.2 Selection: Shared Database + Shared Schema

**Decision:** The "Pool Model" (Shared Schema) was selected for the MVP and initial scaling phases.

**Justification:**

1. **Cost Efficiency:** Minimizes RDS/DB instance overhead by utilizing a single connection pool.
2. **Schema Evolution:** Centralized migrations are executed once, ensuring all tenants are on the latest version of the application logic simultaneously.
3. **Development Velocity:** Seamless integration with modern ORMs like Prisma and TypeORM through standard discriminator patterns.

---

## 2. Technology Stack Selection

The following stack was selected based on the requirements for non-blocking I/O, rapid UI state management, and strict relational integrity.

### 2.1 Backend: Node.js (Express)

* **Rationale:** The Event Loop architecture handles high volumes of I/O-bound requests typical of SaaS project management tools (task updates, notifications).
* **Tenant Isolation:** Implementation of a "Global Tenant Context" via Express middleware to ensure every database transaction is automatically scoped.

### 2.2 Frontend: React (Vite)

* **Rationale:** Component-driven architecture allows for dynamic UI branding based on the authenticated tenant's configuration.
* **Efficiency:** Vite provides significantly faster HMR (Hot Module Replacement) compared to Webpack, accelerating the SDLC.

### 2.3 Database: PostgreSQL 15

* **Rationale:** Support for **Row-Level Security (RLS)** provides a secondary layer of protection against cross-tenant data leaks.
* **Scalability:** Advanced indexing (B-Tree/GIN) and JSONB support allow for future tenant-specific custom fields without schema changes.

---

## 3. Security & Isolation Framework

Data isolation is treated as a core system constraint rather than a feature.

### 3.1 Identity-Centric Isolation

The platform utilizes **Stateless JWTs** to carry the tenant context.

* The `tenant_id` is immutable once the token is signed.
* The Application Tier extracts the `tenant_id` and injects it into the Database Persistence Layer.
* **Constraint:** Direct table access is forbidden; all queries must pass through a repository layer that appends the isolation predicate.

### 3.2 Role-Based Access Control (RBAC)

We utilize a hierarchical permission model to manage access within a single tenant boundary.

| Level | Role | Permissions |
| --- | --- | --- |
| **0** | Super Admin | System-wide health monitoring and global tenant management. |
| **1** | Tenant Owner | Billing, user provisioning, and organizational configuration. |
| **2** | Project Manager | Project creation and resource assignment within their tenant. |
| **3** | Contributor | Task execution and status updates. |

---

## 4. Operational Readiness

### 4.1 Deployment Strategy

The system is fully containerized using Docker, allowing for "Infrastructure as Code" (IaC) principles.

* **Parity:** Developers run the exact same container images as the production environment.
* **Orchestration:** Docker Compose manages the lifecycle of the API, Frontend, and PostgreSQL instances, including health checks and volume persistence.

### 4.2 Risk Mitigation

* **Data Leakage:** Mitigated via automated unit testing of the isolation middleware.
* **Noisy Neighbor:** Monitored via DB-level statement timeouts and application-tier rate limiting.

---
