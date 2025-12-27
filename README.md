# CloudScale SaaS Framework

## Overview
**CloudScale** is a robust, production-ready B2B SaaS boilerplate engineered with a focus on **Enterprise-Grade Multi-Tenancy** and **Isolated Data Architectures**. It provides a scalable foundation for startups requiring complex organizational hierarchies, secure data partitioning, and granular permission management.

Built for high-concurrency environments, this platform ensures that every tenant operates in a virtualized workspace, preventing cross-tenant data leakage while maintaining high system performance.

## Core Capabilities
* **Dynamic Multi-Tenancy:** Automated workspace provisioning with unique UUID-based data isolation.
* **RBAC+ (Advanced Access Control):** Beyond simple roles; supports granular permission sets for Super Admins, Managers, and Contributors.
* **Activity Auditing:** Comprehensive logging of project mutations and user actions per organization.
* **Real-time Synchronization:** WebSocket-ready architecture for instant task and project status updates.
* **Scalable Task Engine:** Advanced project orchestration featuring priority queuing and deadline monitoring.
* **Identity Management:** Secure session handling using HttpOnly cookies and Bcrypt-protected credential storage.
* **Automated Provisioning:** Integrated Docker scripts for one-command deployment of the entire microservice ecosystem.

## Technical Architecture

### **Frontend Interface**
* **Core:** React.js 18 (Functional Components & Hooks)
* **Routing:** React Router v6 (Protected & Public Route Wrappers)
* **Data Fetching:** Axios with Interceptor-based Auth handling
* **UI System:** Modern Modular CSS / PostCSS

### **Backend Services**
* **Engine:** Node.js & Express.js (RESTful Design)
* **Persistence:** PostgreSQL 15+
* **ORM Layer:** Prisma (Type-safe Database Access)
* **Security Suite:** JSON Web Tokens (JWT), CORS policies, and Helmet middleware

---

## System Installation

### **Prerequisites**
* **Docker Desktop** (Engine 20.10+)
* **Node.js LTS** (If running locally)

### **Quick Start (Containerized)**
The most efficient way to spin up the development environment:

1.  **Clone the Source**
    ```bash
    git clone [https://github.com/gowrishjanapareddy/saas_project_5.git](https://github.com/gowrishjanapareddy/saas_project_5.git)
    cd saas_project_5
    ```

2.  **Initialize Environment**
    Create a `.env` file in the root directory:
    ```bash
    # Database Settings
    POSTGRES_USER=dev_admin
    POSTGRES_PASSWORD=dev_password_99
    POSTGRES_DB=cloudscale_db

    # Auth Settings
    JWT_SECRET=generate_your_secure_string_here
    ```

3.  **Boot System**
    ```bash
    docker-compose up -d --build
    ```

4.  **Verification**
    * **Frontend UI:** `http://localhost:3000`
    * **API Gateway:** `http://localhost:5000/api/v1`

---

## API Reference (Version 1)

### **Identity & Access**
| Endpoint | Method | Action |
| :--- | :--- | :--- |
| `/api/v1/auth/signup` | `POST` | Create new tenant & admin user |
| `/api/v1/auth/login` | `POST` | Authenticate and receive session token |

### **Workspace Management**
| Endpoint | Method | Action |
| :--- | :--- | :--- |
| `/api/v1/projects` | `GET` | Fetch all projects for the active tenant |
| `/api/v1/projects` | `POST` | Initialize a new project workspace |
| `/api/v1/tasks/:id` | `PATCH` | Update task lifecycle status |
| `/api/v1/users` | `GET` | (Admin Only) Manage organization members |

---

## Development Credentials

For testing purposes, use the following pre-seeded accounts:

| Role | Username | Password |
| :--- | :--- | :--- |
| **System SuperAdmin** | `root@cloudscale.io` | `CloudScale@2025` |
| **Organization Admin** | `admin@acme-corp.com` | `AcmePass123` |
| **Standard Member** | `user@acme-corp.com` | `UserPass123` |

---


## 📄 License

Distributed under the **MIT License**. See `LICENSE` for more information.
