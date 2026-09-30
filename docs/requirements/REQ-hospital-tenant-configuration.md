# REQ-hospital-tenant-configuration: Hospital Tenant Configuration

**Decision:** dec-go-6 · **Feature:** feat-hospital-tenant-configuration

## Summary
A Hospital Admin can configure everything specific to their hospital (tenant) from one place: departments, packages, rate lists, staff, branding, and operational settings. The behaviour is identical whether the tenant is on PaaS or SaaS.

## Actors
- **Hospital Admin** - owns and edits all tenant configuration.
- **Super Admin** - ratifies onboarding (see REQ-hospital-self-onboarding-application); no configuration edits are possible before this.

## Preconditions
- The hospital's onboarding has been ratified and the tenant is **active**. Configuration is **not** editable before that point (qna q6).

## Scope
All six areas are covered by this single requirement (qna q1).

### 1. Departments
Hospital Admin can add, rename, and deactivate the hospital's departments (e.g. USG, Labour Room, Billing).

### 2. Packages
Hospital Admin can create and edit the hospital's packages, including package caps used by the Summing Engine.

### 3. Rate lists
- Hospital Admin can edit **all rate lists** (the blueprint defines 2) and the **4 delivery categories** (qna q3).
- Edits are **effective immediately**; there is no versioning or effective dating (qna q3).

### 4. Staff
- Hospital Admin can create staff users and assign them roles. Per the clarification, user creation is supported by the Identity Service (qna q4 corrects the earlier blueprint note that it does not create users).
- Assignable roles are the tenant-level roles in the blueprint: USG Staff, Lady Doctor, LR Staff, Billing Reception, RMO, Incident Manager.

### 5. Branding
Hospital Admin can configure the hospital name, logo, colours, and receipt header/footer (qna q5). No file size or format limits were specified; standard image upload validation applies.

### 6. Operational settings
Hospital Admin can configure **working hours** (qna q2). No other operational settings are in scope for this requirement.

## Acceptance criteria
1. A Hospital Admin of an active tenant can open a configuration area and save changes for each of the six areas.
2. A Hospital Admin of a tenant not yet ratified cannot edit any configuration.
3. Rate list and delivery category edits apply to the next charge calculation immediately.
4. Configuration is scoped strictly to the admin's own tenant.
5. Non-admin roles cannot edit configuration.

## Open items (not blocking)
- Additional operational settings beyond working hours (only "working hours" was answered).
- File size/format limits for branding uploads.
