# Technical Specification Document (TSD)

**Application:** SaaS Project Management with Multi-Tenancy  
**Created:** October 26, 2025  
**Revision:** v1.0  
**Development Status:** Greenlit for Coding  

---

## 1️. Stakeholder Profiles

Detailed analysis of the three distinct user archetypes that drive platform design decisions.

---

### Type A: Global System Operator (Super Admin)

**Position Overview:**  
Global overseer of the SaaS infrastructure, operating outside tenant boundaries to manage platform-wide operations.

**Operational Scope:**
- System diagnostics and capacity planning
- Billing tier management and quota enforcement
- Risk mitigation through tenant suspension
- VIP client provisioning

**Strategic Priorities:**
- Infrastructure stability and economic viability
- Tenant acquisition and retention optimization
- Fraud detection and platform protection

**Operational Gaps:**
- "Resource utilization per tenant is opaque."
- "Aggregate growth metrics are hard to surface."
- "Billing changes require risky manual DB edits."

---

### Type B: Company Workspace Lead (Tenant Admin)

**Position Overview:**  
Single-organization administrator controlling internal team structure and access.

**Operational Scope:**
- Workspace configuration and visual identity
- Workforce onboarding/offboarding workflows
- Permission matrix administration
- Project portfolio oversight

**Strategic Priorities:**
- Team velocity maximization
- Data sovereignty assurance
- Capacity compliance monitoring

**Operational Gaps:**
- "Team workload visibility is insufficient."
- "Employee ramp-up cycles are protracted."
- "Offboarded staff access lingers."

---

### Type C: Individual Contributor (Team Member)

**Position Overview:**  
Daily platform user focused on personal productivity and task delivery.

**Operational Scope:**
- Task lifecycle management
- Project collaboration participation
- Deadline adherence tracking
- Status reporting obligations

**Strategic Priorities:**
- Consistent delivery against commitments
- Priority clarity without ambiguity
- Minimal process friction

**Operational Gaps:**
- "UI complexity obscures personal workload."
- "Time-sensitive obligations get buried."
- "Project assets are difficult to surface."

---

## 2️. Capability Matrix

Concrete implementation requirements grouped by system domain.

---

### Identity & Access Management

- **CAP-001:** Self-registration with org identifier, subdomain, and initial admin provisioning
- **CAP-002:** JWT stateless auth with 24hr TTL
- **CAP-003:** Three-tier RBAC: platform/global/tenant/user
- **CAP-004:** Mandatory tenant scoping on scoped operations
- **CAP-005:** Client token revocation support

---

### Tenant Lifecycle

- **CAP-006:** Default freemium tier (5 seats, 3 workspaces)
- **CAP-007:** Admin dashboard with tenant directory
- **CAP-008:** Plan/status reconfiguration authority
- **CAP-009:** Hard limits on resource expansion

---

### Workforce Administration

- **CAP-010:** Seat-constrained user provisioning
- **CAP-011:** Tenant-scoped email deduplication
- **CAP-012:** Instant access revocation capability
- **CAP-013:** Self-profile management (non-role)

---

### Workspace Management

- **CAP-014:** Workspace instantiation with metadata
- **CAP-015:** Aggregated workspace analytics view
- **CAP-016:** Recursive workspace cleanup

---

### Work Item Management

- **CAP-017:** Rich work item creation (metadata complete)
- **CAP-018:** Intra-tenant assignment constraints
- **CAP-019:** Atomic status mutation endpoint
- **CAP-020:** Multi-dimensional work item discovery

---

## 3️. System Qualities

Engineering constraints spanning reliability, security, and operations.

- **QUAL-001 (Latency):** P95 < 200ms @ 100rps  
- **QUAL-002 (Crypto):** Bcrypt@10+ for credential storage  
- **QUAL-003 (Elasticity):** Docker-native horizontal capacity  
- **QUAL-004 (Observability):** Proactive DB connectivity validation  
- **QUAL-005 (Operations):** docker-compose single-command deploy  
- **QUAL-006 (Adaptivity):** Fluid mobile/desktop rendering
