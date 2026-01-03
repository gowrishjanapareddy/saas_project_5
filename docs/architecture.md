## System Architecture Definition: Multi-Tenant Project Management SaaS

**Date:** October 26, 2025

**Version:** 1.0

**Status:** Approved

**Author:** Lead Developer / AWS Architecture Team

---

## 1. Executive Summary

This document outlines the architectural framework for a containerized, multi-tenant Project Management System. The architecture prioritizes **logical tenant isolation**, **horizontal scalability**, and a **stateless application tier** to support high-availability SaaS operations.

---

## 2. High-Level System Architecture

The platform utilizes a **containerized three-tier web architecture**. All components are orchestrated via Docker, ensuring environment parity across the SDLC.

### 2.1 Component Overview

* **Client Tier:** A decoupled React.js Single Page Application (SPA).
* **Application Tier:** A stateless Node.js REST API enforcing business logic and RBAC.
* **Data Tier:** A high-performance PostgreSQL 15 relational store.

### 2.2 Network & Security Topology

Communication between tiers is governed by a virtual bridge network. Security is enforced through a **Defense in Depth** strategy:

1. **Identity:** JWT-based stateless authentication.
2. **Access:** Middleware-driven Role-Based Access Control (RBAC).
3. **Data:** Row-Level Security (RLS) patterns using `tenant_id` discriminators.

---

## 3. Multi-Tenancy & Data Isolation Strategy

The system adopts a **Shared Database, Shared Schema** model. This maximizes resource efficiency while maintaining strict logical boundaries.

### 3.1 Tenant Identification

Tenants are identified at the entry point through two primary mechanisms:

* **Subdomain Resolution:** `https://{tenant}.saas-platform.com`
* **Context Injection:** The `tenant_id` is embedded within the JWT claims upon authentication, preventing "ID Spoofing" during API requests.

### 3.2 Data Segregation

Every tenant-specific table contains a `tenant_id` (UUID) foreign key.

* **Query Filtering:** All backend Repository/DAO layers inject a mandatory `WHERE tenant_id = current_user_tenant` clause.
* **Foreign Keys:** Referential integrity is enforced at the database level to ensure data belonging to Tenant A can never reference data from Tenant B.

---

## 4. Data Architecture (ERD)

The schema is optimized for Third Normal Form (3NF) to ensure data consistency and reduce redundancy.

### Core Entity Definitions

* **Tenants:** The root entity managing subscriptions and global configuration.
* **Users:** Bound to a specific tenant; supports unique email constraints per tenant scope.
* **Projects/Tasks:** The primary workload entities, logically partitioned by `tenant_id`.
* **Audit Logs:** Immutable records for compliance and security monitoring.

---

## 5. API Design & Integration

The backend follows RESTful principles with standard HTTP verbs and status codes.

### 5.1 Global Response Envelope

To ensure predictable consumption by the frontend, all responses follow this structure:

```json
{
  "success": boolean,
  "message": "Human-readable summary",
  "data": { "..." },
  "metadata": { "timestamp": "ISO-8601", "traceId": "uuid" }
}

```

### 5.2 Module Catalog

| Domain | Responsibility | Security Level |
| --- | --- | --- |
| **Auth** | Identity, Registration, Session Management | Public / Authenticated |
| **Admin** | Subscription & Tenant Meta-configuration | `super_admin` only |
| **Users** | Provisioning & Profile Management | `tenant_admin` / Self |
| **Work** | Project & Task Lifecycle | Tenant Member |

---

## 6. Deployment & Orchestration

### Container Strategy

* **Frontend:** Nginx-backed container serving optimized React static assets.
* **Backend:** Node.js Alpine-based image for minimal attack surface.
* **Database:** PostgreSQL 15 with persistent volume mapping for data durability.

### Port Configuration

| Service | Internal Port | External Port | Protocol |
| --- | --- | --- | --- |
| Frontend | 3000 | 80/443 | HTTP/S |
| Backend API | 5000 | 5000 | HTTP |
| Database | 5432 | 5432 | TCP |

---
