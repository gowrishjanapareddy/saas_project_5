# Multi-Tenant SaaS Platform 

A **production-grade B2B SaaS application** showcasing **Multi-Tenancy**, **Strict Data Isolation**, and **Role-Based Access Control (RBAC)**.  
This project is designed to demonstrate **real-world SaaS architecture**, backend security practices, and scalable system design.

---

## Overview

The platform allows multiple organizations (**Tenants**) to securely operate within the same system while ensuring **complete data isolation**.  
Each tenant manages its own users, projects, and tasks, while a **Super Admin** oversees the entire system.

### Core Objectives
- Demonstrate **multi-tenant SaaS architecture**
- Enforce **tenant-level data isolation**
- Implement **secure authentication & authorization**
- Showcase **RBAC-driven access control**
- Provide a **scalable, containerized setup**

---

## User Roles & Access

| Role | Permissions |
|-----|------------|
| **Super Admin** | Manage all tenants, view global system data |
| **Tenant Admin** | Manage users, projects, and tasks within their tenant |
| **Standard User** | Work on assigned projects and tasks only |

---

## Key Features

- **Multi-Tenancy Architecture**  
  Shared database with strict tenant-based data isolation using `tenant_id`.

- **Secure Authentication**  
  JWT-based authentication with Bcrypt password hashing.

- **Role-Based Access Control (RBAC)**  
  Middleware-enforced permissions per API endpoint.

- **Project Management**  
  Tenant-scoped projects with status tracking.

- **Task Orchestration**  
  Task assignments with priorities and deadlines.

- **Team Management**  
  Tenant Admins can onboard and manage users.

- **Super Admin Dashboard**  
  Centralized monitoring of all tenants.

- **Modern Responsive UI**  
  Built with React 18 and responsive layouts.

---

## Technology Stack

### Frontend
- **React.js (v18)**
- React Context API
- React Router DOM (v6)
- Axios
- CSS3 (Flexbox & Grid)

### Backend
- **Node.js (v18)**
- Express.js
- Prisma ORM
- JWT Authentication
- Bcrypt
- Express Validator
- CORS

### Database & DevOps
- **PostgreSQL (v15)**
- Docker & Docker Compose
- Linux Alpine Containers

---

## Architecture Overview

The system follows a **Client–Server Architecture**:

- React frontend communicates with the backend via REST APIs.
- Express backend handles authentication, RBAC, and tenant scoping.
- PostgreSQL stores all data with mandatory `tenant_id` filtering.
- Prisma ORM enforces structured and secure data access.
- Docker Compose orchestrates services for easy deployment.

---

## Installation & Setup

### Prerequisites
- Docker & Docker Compose (**Recommended**)
- Node.js v18+ (for local setup)
- PostgreSQL (for local setup)

---

### Method 1: Docker (Recommended)

This method sets up **Frontend, Backend, and Database automatically**.

1. **Clone the Repository**
```bash
git clone <your-repo-url>
cd Multi-Tenant-SaaS-Platform
```

2. **Environment Configuration**
Create a `.env` file in the root directory:
```env
POSTGRES_USER=postgres
POSTGRES_PASSWORD=postgres
POSTGRES_DB=saas_db
```

3. **Start the Application**
```bash
docker-compose up -d --build
```

4. **Access Services**
- Frontend: http://localhost:3000
- Backend Health: http://localhost:5000/api/health

> Migrations and seed data run automatically on startup.

---

### Method 2: Local Development

1. **Database**
Ensure PostgreSQL is running on port `5432`.

2. **Backend**
```bash
cd backend
npm install
cp .env.example .env
npx prisma migrate dev --name init
npm run seed
npm start
```

3. **Frontend**
```bash
cd frontend
npm install
npm start
```

---

## Environment Variables

| Variable | Description | Default (Docker) |
|--------|-------------|-----------------|
| PORT | Backend port | 5000 |
| DATABASE_URL | PostgreSQL connection | docker service |
| JWT_SECRET | JWT signing secret | Required |
| FRONTEND_URL | CORS origin | http://localhost:3000 |

---

## API Endpoints

### Authentication
- `POST /api/auth/register-tenant`
- `POST /api/auth/login`

### Projects & Tasks
- `GET /api/projects`
- `POST /api/projects`
- `POST /api/projects/:id/tasks`
- `PATCH /api/tasks/:id/status`

### Management
- `GET /api/tenants` (Super Admin)
- `GET /api/tenants/:id/users` (Tenant Admin)
- `POST /api/tenants/:id/users` (Tenant Admin)

---

## Seeded Test Accounts

### Super Admin
- **Email:** superadmin@system.com
- **Password:** Admin@123

### Tenant Admin (Demo Company)
- **Email:** admin@demo.com
- **Password:** Demo@123
- **Subdomain:** demo

---

## Deployment Ready

- Fully containerized
- Environment-driven configuration
- Production-grade architecture
- Scalable multi-tenant design

---

## License
This project is intended for **educational and portfolio purposes**.

---

## Contribution
Pull requests and improvements are welcome.  
This project is ideal for showcasing **SaaS architecture**, **RBAC**, and **multi-tenant system design** in interviews and portfolios.
