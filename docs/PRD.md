# Product Requirements Document (PRD)

**Project Title:** Multi-Tenant SaaS Project Management Platform  
**Date:** October 26, 2025  
**Version:** 1.0  
**Status:** Approved for Development  

---

## 1️. User Personas

This section outlines the three main categories of users who will engage with the platform. Clearly defining these personas helps shape the product experience to address their unique goals, workflows, and challenges.

---

### Persona 1: Super Admin (Platform Owner)

**Role Description:**  
This individual is responsible for managing the entire SaaS ecosystem. The Super Admin oversees technical operations, monitors platform usage, and maintains tenant accounts. Unlike other users, this role isn't tied to a specific tenant.

**Key Responsibilities:**
- Monitor overall system performance and database health
- Manage tenant subscriptions and plan upgrades/downgrades
- Suspend or remove non-compliant or inactive tenants
- Handle enterprise-level client onboarding manually when needed

**Primary Goals:**
- Maintain system reliability and cost efficiency  
- Maximize the number of active paying tenants  
- Detect and prevent fraudulent or abusive use of the platform  

**Pain Points:**
- “I lack tools to identify which tenants consume the most resources.”  
- “It’s hard to visualize global user growth and usage trends.”  
- “Updating subscription tiers directly in the database feels risky and error-prone.”  

---

### Persona 2: Tenant Admin (Organization Manager)

**Role Description:**  
A Tenant Admin oversees one organization’s workspace. They handle administrative tasks such as managing users, projects, and settings within their tenant’s environment.

**Key Responsibilities:**
- Manage tenant details (name, domain, branding)
- Add, remove, and manage users within the subscription limits  
- Set user roles and permissions  
- Supervise all projects and their progress  

**Primary Goals:**
- Manage team performance efficiently  
- Secure sensitive company data  
- Ensure the organization stays within plan restrictions  

**Pain Points:**
- “I don’t have an at-a-glance view of team activity.”  
- “It takes too long to onboard new hires.”  
- “I worry ex-employees might still have access to company data.”  

---

### Persona 3: End User (Employee)

**Role Description:**  
A standard user who relies on the platform daily to execute assigned tasks and report work progress.

**Key Responsibilities:**
- Create, manage, and update individual tasks  
- Collaborate on project deliverables with teammates  
- Track upcoming deadlines and task completion progress  
- Provide updates or reports to supervisors  

**Primary Goals:**
- Finish assigned tasks on schedule  
- Understand assigned responsibilities clearly  
- Spend minimal time on non-essential admin tasks  

**Pain Points:**
- “I get lost in cluttered dashboards—I only want to see my own tasks.”  
- “I miss due dates because deadlines aren’t clearly visible.”  
- “Important project files are hard to locate quickly.”  

---

## 2️. Functional Requirements

This section defines the product’s required behavior, covering how each module should operate within the system.

---

### Module: Authentication & Authorization

- **FR-001:** Allow organizations to register new tenants with a name, subdomain, and designated admin credentials.  
- **FR-002:** Implement stateless JSON Web Token (JWT) authentication with a 24-hour token lifespan.  
- **FR-003:** Employ Role-Based Access Control (RBAC) for three roles: `super_admin`, `tenant_admin`, and `user`.  
- **FR-004:** Validate `tenant_id` in every API request to ensure tenants cannot access each other’s data.  
- **FR-005:** Provide a logout mechanism that clears the token client-side.  

---

### Module: Tenant Management

- **FR-006:** Assign each new tenant a **Free** plan by default (limits: 5 users, 3 projects).  
- **FR-007:** Enable Super Admins to view an indexed or paginated list of tenants.  
- **FR-008:** Permit Super Admins to modify tenant details, including subscription level and status.  
- **FR-009:** Enforce subscription usage caps when users attempt to add new resources.  

---

### Module: User Management

- **FR-010:** Allow Tenant Admins to create users, constrained by their tenant’s subscription limit.  
- **FR-011:** Maintain unique email addresses within the context of each tenant.  
- **FR-012:** Offer a feature for Tenant Admins to deactivate users and revoke login access immediately.  
- **FR-013:** Permit users to view and update personal profiles but prohibit self-role changes.  

---

### Module: Project Management

- **FR-014:** Support creation of projects with `name`, `description`, and `status` fields.  
- **FR-015:** Provide an overview dashboard summarizing all projects with related task statistics.  
- **FR-016:** Allow permanent deletion of projects, removing all linked tasks as part of a cascade deletion.  

---

### Module: Task Management

- **FR-017:** Enable tasks to be created with `title`, `description`, `priority`, and `dueDate` fields.  
- **FR-018:** Allow assigning tasks only to users within the same tenant.  
- **FR-019:** Provide a dedicated API endpoint for updating task status independently.  
- **FR-020:** Implement task filtering by status, priority, and assigned user.  

---

## 3️. Non-Functional Requirements

This section describes system-wide characteristics such as performance, security, scalability, and deployment quality.

- **NFR-001 (Performance):**  
  95% of API responses must complete within 200ms when serving up to 100 concurrent users.  

- **NFR-002 (Security):**  
  All passwords must be securely hashed using Bcrypt with a minimum of 10 salt rounds.  

- **NFR-003 (Scalability):**  
  The system must support horizontal scaling through Docker-based container orchestration.  

- **NFR-004 (Availability):**  
  The database layer should actively monitor and report connection health.  

- **NFR-005 (Portability):**  
  The entire application stack must be deployable using a single `docker-compose up -d` command.  

- **NFR-006 (Usability):**  
  The front-end should remain fully responsive across mobile (<768px) and desktop devices.  
