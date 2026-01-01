# Multi-Tenant SaaS Platform – API Reference

This document describes the REST API for a multi-tenant project management platform, including authentication, tenant administration, user management, projects, and tasks.

---

## Authentication & Security

- **Auth Scheme:** Bearer authentication using JSON Web Tokens (JWT)  
- **HTTP Header (required for protected routes):**  
  `Authorization: Bearer <access_token>`  
- **Access Token Lifetime:** 24 hours per issued token  
- **Base URL (Local Dev):**  
  `http://localhost:5000/api`

All endpoints that are not explicitly marked as **Public** require a valid JWT in the `Authorization` header. [web:21][web:39]

---

## 1. System

### 1.1 Health Check

Use this endpoint to quickly verify that the API is reachable and the database is online.

- **Method & Path:** `GET /health`  
- **Visibility:** Public (no token required)

#### Example Success Response (200 OK)

```json
{
  "status": "ok",
  "database": "connected"
}
```

---

## 2. Authentication

### 2.1 Register Tenant (Initial Onboarding)

Creates a brand-new **tenant account** and the corresponding **first admin user** in a single step.

- **Method & Path:** `POST /auth/register-tenant`  
- **Visibility:** Public

#### Request Body

```json
{
  "tenantName": "Acme Corp",
  "subdomain": "acme",
  "adminEmail": "admin@acme.com",
  "password": "SecurePassword123"
}
```

#### Response (201 Created)

```json
{
  "message": "Tenant registered successfully",
  "tenantId": "uuid-string"
}
```

---

### 2.2 Login (Obtain JWT)

Authenticates a user with email and password and returns a signed JWT to be used in subsequent requests.

- **Method & Path:** `POST /auth/login`  
- **Visibility:** Public

#### Request Body

```json
{
  "email": "admin@acme.com",
  "password": "SecurePassword123"
}
```

#### Response (200 OK)

```json
{
  "token": "eyJhbGciOiJIUzI1NiIsIn...",
  "user": {
    "id": "uuid",
    "email": "admin@acme.com",
    "role": "tenant_admin",
    "tenantId": "uuid"
  }
}
```

---

### 2.3 Get Current User (Session Introspection)

Returns the profile of the user associated with the provided JWT.

- **Method & Path:** `GET /auth/me`  
- **Visibility:** Protected (any authenticated role)

#### Response (200 OK)

```json
{
  "user": {
    "id": "uuid",
    "fullName": "John Doe",
    "email": "john@acme.com",
    "role": "user"
  }
}
```

---

## 3. Tenant Operations (Super Admin Scope)

These endpoints are intended for **platform-level** administrators managing all tenants.

### 3.1 List All Tenants

Fetches a catalog of every tenant registered in the system.

- **Method & Path:** `GET /tenants`  
- **Visibility:** Protected – `super_admin` only

#### Response (200 OK)

```json
{
  "status": "success",
  "results": 2,
  "data": {
    "tenants": [
      { "id": "1", "name": "Acme Corp", "subdomain": "acme" },
      { "id": "2", "name": "Beta Inc", "subdomain": "beta" }
    ]
  }
}
```

---

### 3.2 Get Tenant by ID

Retrieves metadata for a specific tenant.

- **Method & Path:** `GET /tenants/:id`  
- **Visibility:** Protected – `super_admin` only

#### Response (200 OK)

```json
{
  "id": "uuid",
  "name": "Acme Corp",
  "status": "active"
}
```

---

### 3.3 Update Tenant Configuration

Allows editing of core tenant properties, such as name or lifecycle status.

- **Method & Path:** `PUT /tenants/:id`  
- **Visibility:** Protected – `super_admin` only

#### Request Body

```json
{
  "name": "Acme Global",
  "status": "inactive"
}
```

---

## 4. User Management (Within a Tenant)

These endpoints are used by a **Tenant Admin** to manage members of their own organization.

### 4.1 Get Users for a Tenant

Returns all users currently associated with the specified tenant.

- **Method & Path:** `GET /tenants/:tenantId/users`  
- **Visibility:** Protected – `tenant_admin`

#### Response (200 OK)

```json
{
  "data": {
    "users": [
      { "id": "u1", "fullName": "Alice", "role": "user" }
    ]
  }
}
```

---

### 4.2 Create Tenant User

Adds a new user account under a given tenant.

- **Method & Path:** `POST /tenants/:tenantId/users`  
- **Visibility:** Protected – `tenant_admin`

#### Request Body

```json
{
  "email": "alice@acme.com",
  "password": "Password123",
  "fullName": "Alice Smith",
  "role": "user"
}
```

---

### 4.3 Update Tenant User

Updates the details or role of an existing user.

- **Method & Path:** `PUT /users/:id`  
- **Visibility:** Protected – `tenant_admin`

#### Request Body

```json
{
  "fullName": "Alice Jones",
  "role": "tenant_admin"
}
```

---

### 4.4 Delete Tenant User

Removes a user from the tenant so they can no longer access the workspace.

- **Method & Path:** `DELETE /users/:id`  
- **Visibility:** Protected – `tenant_admin`

---

## 5. Project Management

Project endpoints operate within the scope of the current tenant.

### 5.1 List Projects for Current Tenant

Lists all projects visible to the authenticated user’s tenant.

- **Method & Path:** `GET /projects`  
- **Visibility:** Protected – `user` or `admin`

#### Response (200 OK)

```json
{
  "data": {
    "projects": [
      { "id": "p1", "title": "Website Redesign", "status": "active" }
    ]
  }
}
```

---

### 5.2 Create a New Project

Registers a new project instance under the tenant context.

- **Method & Path:** `POST /projects`  
- **Visibility:** Protected – `admin`

#### Request Body

```json
{
  "title": "Q3 Marketing Campaign",
  "description": "Planning for Q3",
  "status": "active"
}
```

---

### 5.3 Get Project by ID

Returns the details for a specific project.

- **Method & Path:** `GET /projects/:id`  
- **Visibility:** Protected – `user` or `admin`

---

### 5.4 Update Project

Modifies the metadata or status of an existing project.

- **Method & Path:** `PUT /projects/:id`  
- **Visibility:** Protected – `admin`

#### Request Body

```json
{
  "status": "completed"
}
```

> **Hint:**  
> To support hard deletion of a project, expose:  
> `DELETE /projects/:id`

---

## 6. Task Management

Tasks are always created within the context of a specific project.

### 6.1 List Tasks for a Project

Retrieves all tasks belonging to a particular project.

- **Method & Path:** `GET /projects/:projectId/tasks`  
- **Visibility:** Protected – `user` or `admin`

#### Response (200 OK)

```json
{
  "data": {
    "tasks": [
      { "id": "t1", "title": "Draft content", "status": "TODO" }
    ]
  }
}
```

---

### 6.2 Create Task in Project

Adds a new task item inside a project.

- **Method & Path:** `POST /projects/:projectId/tasks`  
- **Visibility:** Protected – `admin`

#### Request Body

```json
{
  "title": "Fix Header Bug",
  "description": "CSS issue on mobile",
  "priority": "HIGH",
  "dueDate": "2023-12-31"
}
```

---

### 6.3 Change Task Status (Partial Update)

Provides a focused endpoint to change only the status of an existing task, which is ideal for Kanban-style interactions.

- **Method & Path:** `PATCH /tasks/:id/status`  
- **Visibility:** Protected – `user` or `admin`

#### Request Body

```json
{
  "status": "IN_PROGRESS"
}
```

---

### 6.4 Update Task (Full Update)

Allows full editing of a task’s fields such as title, priority, and other attributes.

- **Method & Path:** `PUT /tasks/:id`  
- **Visibility:** Protected – `admin`

#### Request Body

```json
{
  "title": "Fix Header Bug (Updated)",
  "priority": "MEDIUM"
}
```
