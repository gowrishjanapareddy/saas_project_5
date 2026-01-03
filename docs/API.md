# Multi-Tenant SaaS Platform – API Reference 

This document provides a **comprehensive REST API reference** for the **Multi-Tenant SaaS Project Management Platform**.  
It is intended for **backend developers, frontend integrators, and system architects** who need a clear contract for interacting with the platform.

---

## Authentication & Security

- **Authentication Scheme:** Bearer Token (JWT)
- **Header Format:**
  ```http
  Authorization: Bearer <access_token>
  ```
- **Token Validity:** 24 hours
- **Base URL (Local Development):**
  ```
  http://localhost:5000/api
  ```

 All endpoints are **protected by default** unless explicitly marked as **Public**.

---

## 1. System

### 1.1 Health Check

Verify API availability and database connectivity.

- **Method:** `GET`
- **Endpoint:** `/health`
- **Access:** Public

#### Response (200 OK)
```json
{
  "status": "ok",
  "database": "connected"
}
```

---

## 2. Authentication

### 2.1 Register Tenant (Initial Onboarding)

Creates a new tenant and the first tenant admin user.

- **Method:** `POST`
- **Endpoint:** `/auth/register-tenant`
- **Access:** Public

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

### 2.2 Login (JWT Issuance)

Authenticates a user and returns a signed JWT.

- **Method:** `POST`
- **Endpoint:** `/auth/login`
- **Access:** Public

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

### 2.3 Get Current User

Returns the authenticated user's profile.

- **Method:** `GET`
- **Endpoint:** `/auth/me`
- **Access:** Protected (any authenticated role)

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

## 3. Tenant Operations (Super Admin Only)

### 3.1 List All Tenants

- **Method:** `GET`
- **Endpoint:** `/tenants`
- **Role Required:** `super_admin`

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

- **Method:** `GET`
- **Endpoint:** `/tenants/:id`
- **Role Required:** `super_admin`

```json
{
  "id": "uuid",
  "name": "Acme Corp",
  "status": "active"
}
```

---

### 3.3 Update Tenant

- **Method:** `PUT`
- **Endpoint:** `/tenants/:id`
- **Role Required:** `super_admin`

```json
{
  "name": "Acme Global",
  "status": "inactive"
}
```

---

## 4. User Management (Tenant Scope)

### 4.1 List Tenant Users

- **Method:** `GET`
- **Endpoint:** `/tenants/:tenantId/users`
- **Role Required:** `tenant_admin`

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

- **Method:** `POST`
- **Endpoint:** `/tenants/:tenantId/users`
- **Role Required:** `tenant_admin`

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

- **Method:** `PUT`
- **Endpoint:** `/users/:id`
- **Role Required:** `tenant_admin`

```json
{
  "fullName": "Alice Jones",
  "role": "tenant_admin"
}
```

---

### 4.4 Delete Tenant User

- **Method:** `DELETE`
- **Endpoint:** `/users/:id`
- **Role Required:** `tenant_admin`

---

## 5. Project Management

### 5.1 List Projects

- **Method:** `GET`
- **Endpoint:** `/projects`
- **Access:** `user`, `admin`

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

### 5.2 Create Project

- **Method:** `POST`
- **Endpoint:** `/projects`
- **Access:** `admin`

```json
{
  "title": "Q3 Marketing Campaign",
  "description": "Planning for Q3",
  "status": "active"
}
```

---

### 5.3 Update Project

- **Method:** `PUT`
- **Endpoint:** `/projects/:id`
- **Access:** `admin`

```json
{
  "status": "completed"
}
```

---

## 6. Task Management

### 6.1 List Tasks

- **Method:** `GET`
- **Endpoint:** `/projects/:projectId/tasks`

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

### 6.2 Create Task

- **Method:** `POST`
- **Endpoint:** `/projects/:projectId/tasks`
- **Access:** `admin`

```json
{
  "title": "Fix Header Bug",
  "description": "CSS issue on mobile",
  "priority": "HIGH",
  "dueDate": "2023-12-31"
}
```

---

### 6.3 Update Task Status

- **Method:** `PATCH`
- **Endpoint:** `/tasks/:id/status`

```json
{
  "status": "IN_PROGRESS"
}
```

---

### 6.4 Update Task (Full)

- **Method:** `PUT`
- **Endpoint:** `/tasks/:id`
- **Access:** `admin`

```json
{
  "title": "Fix Header Bug (Updated)",
  "priority": "MEDIUM"
}
```

---

## Notes

- All queries are automatically **scoped by tenantId**
- RBAC is enforced at the **middleware level**
- Designed for **scalability, security, and SaaS compliance**

---

## License

This API documentation is provided for **educational and portfolio purposes**.
