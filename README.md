# CloudScale: Enterprise Multi-Tenant SaaS Framework

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Node.js](https://img.shields.io/badge/node-%3E%3D%2018.0.0-brightgreen)](https://nodejs.org/)
[![Docker](https://img.shields.io/badge/docker-supported-blue)](https://www.docker.com/)
[![Prisma](https://img.shields.io/badge/ORM-Prisma-2D3748)](https://www.prisma.io/)

---

## Project Overview

CloudScale is a production-ready Enterprise Multi-Tenant SaaS Framework designed for startups and teams building B2B platforms.

It solves the hardest SaaS problems out-of-the-box:

- Multi-Tenancy
- Strict Data Isolation
- Role-Based Access Control (RBAC)
- Audit Logging
- Subscription Billing (Stripe)

CloudScale follows a Shared Database, Isolated Rows architecture, allowing thousands of tenants to safely coexist inside a single PostgreSQL database while guaranteeing logical and physical data separation.

---

## System Architecture & Request Flow

Request → Tenant Context pipeline:

1. Tenant ID extracted from JWT or trusted request header  
2. User authorization verified against tenant  
3. Prisma automatically scopes queries using tenant_id  
4. Only tenant-isolated data is returned  

Tenant A can never access Tenant B data.

---

## Core Capabilities

- Dynamic Tenant Provisioning
- Advanced RBAC (Super Admin, Tenant Admin, User)
- Immutable Audit Logs
- Real-Time WebSocket Sync
- Stripe Subscription Billing
- Docker-First Deployment

---

## Project Structure

```text
.
├── backend/
│   ├── prisma/
│   │   ├── schema.prisma
│   │   └── migrations/
│   ├── src/
│   │   ├── middleware/
│   │   ├── controllers/
│   │   ├── routes/
│   │   ├── services/
│   │   └── utils/
│   └── Dockerfile
├── frontend/
│   ├── src/
│   │   ├── components/
│   │   ├── hooks/
│   │   ├── context/
│   │   └── pages/
│   └── Dockerfile
├── docker-compose.yml
└── README.md
```

---

## Installation & Setup

### Quick Start (Docker – Recommended)

```bash
git clone https://github.com/gowrishjanapareddy/saas_project_5.git
cd saas_project_5

cat <<EOF > .env
POSTGRES_USER=dev_admin
POSTGRES_PASSWORD=dev_password_99
POSTGRES_DB=cloudscale_db
DATABASE_URL=postgresql://dev_admin:dev_password_99@postgres:5432/cloudscale_db
JWT_SECRET=your_32_character_random_string
STRIPE_SECRET_KEY=sk_test_your_key_here
EOF

docker-compose up -d --build

docker-compose exec backend npx prisma migrate dev --name init
docker-compose exec backend npm run seed
```

---

### Manual Development Setup (Without Docker)

```bash
cd backend
npm install
npx prisma generate
npx prisma migrate dev
npm run dev

cd frontend
npm install
npm start
```

---

## Stripe Integration

```text
Webhook Endpoint: /api/v1/webhooks/stripe
Plan Config: backend/src/config/plans.ts
Frontend Hook: useCheckout()
```

---

## API Reference (v1)

```text
POST /api/v1/auth/signup   - Register Tenant + Admin
POST /api/v1/auth/login    - Authenticate and receive JWT
GET  /api/v1/projects      - Fetch tenant projects
GET  /api/v1/users         - List organization members
```

All endpoints are automatically tenant-scoped.

---

## Development Credentials

```text
System Super Admin
Email: root@cloudscale.io
Password: CloudScale@2025

Organization Admin
Email: admin@acme-corp.com
Password: AcmePass123

Standard User
Email: user@acme-corp.com
Password: UserPass123
```

---

## Contribution Guide

```bash
git checkout -b feature/AmazingFeature
git commit -m "feat: add amazing feature"
git push origin feature/AmazingFeature
```

Open a Pull Request on GitHub.

---

## License

MIT License

---

## Support

If this project helps you, please star the repository.

Happy Building 
