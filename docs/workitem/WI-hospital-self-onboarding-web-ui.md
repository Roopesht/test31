# WI-hospital-self-onboarding-web-ui — Hospital Self-Onboarding Web UI

- **Requirement:** [REQ-hospital-self-onboarding-application](../requirements/REQ-hospital-self-onboarding-application.md) ([mock](../requirements/REQ-hospital-self-onboarding-application/mock.md))
- **Application:** web-ui (React)
- **Sibling work item:** WI-hospital-self-onboarding-api (Express). It owns every endpoint listed below.

## Scope

Build the four screens in the requirement's mock:

1. Hospital application form (public, unauthenticated), FR-1, FR-2, FR-3
2. Post-submit "Temporarily Approved" page leading into the onboarding setup, FR-4
3. Super Admin review queue and application detail, with Ratify / Stop service, FR-5
4. "Service stopped" handling on sign-in and on any authenticated request, FR-5

**Out of scope:** the setup steps themselves (Departments, Packages, Rate lists & branding,
Go live). This work item only links to the first step. The 250k milestone (FR-6) has no UI.

## Routes

| Route | Access | Screen |
|---|---|---|
| `/register` | Public | Application form |
| `/onboarding/welcome` | Hospital Admin | Post-submit page with the setup stepper and a "Start setup" CTA |
| `/super-admin/hospital-applications` | Super Admin | Review queue |
| `/super-admin/hospital-applications/:hospitalId` | Super Admin | Application detail + certificate preview |
| `/login` (existing or new) | Public | Shows the service-stopped message |

Add a "Register your hospital" link to the login page that points to `/register`.

## API contract consumed

The API work item owns this contract. It's written here so both work items build against the
same shape, and any change must be agreed with that work item.

| Method & path | Purpose | Notes |
|---|---|---|
| `POST /api/onboarding/applications` | Submit application | `multipart/form-data`: `hospitalName`, `registrationNumber`, `address`, `contactPerson`, `phone`, `email`, `password`, `bedCount`, `certificate` (file). `201` → `{ hospitalId, status: "temporarily_approved" }`. `400` → `{ errors: { <field>: <message> } }`. `409` → email or registration number already registered |
| `POST /api/auth/login` | Sign in (Identity Service) | Called right after a `201` with the same email/password so the admin lands signed in. `403 { code: "SERVICE_STOPPED" }` when the tenant is stopped |
| `GET /api/admin/hospital-applications?status=` | Queue | `status` ∈ `temporarily_approved`, `ratified`, `service_stopped` |
| `GET /api/admin/hospital-applications/:hospitalId` | Detail | Includes `certificateUrl` (short-lived signed S3 URL) and review history |
| `POST /api/admin/hospital-applications/:hospitalId/ratify` | Ratify | `200` → updated application |
| `POST /api/admin/hospital-applications/:hospitalId/stop` | Stop service | `200` → updated application |

Any authenticated call that returns `403 { code: "SERVICE_STOPPED" }` must be handled globally
(see "Service stopped handling" below).

## Implementation

### Suggested structure

```
src/
  features/onboarding/
    RegisterHospitalPage.tsx
    registerHospitalSchema.ts       # field + file validation rules
    OnboardingWelcomePage.tsx
    SetupStepper.tsx
    api.ts                          # submitApplication()
  features/superAdmin/hospitalApplications/
    HospitalApplicationsPage.tsx
    HospitalApplicationDetailPage.tsx
    StopServiceConfirmDialog.tsx
    api.ts                          # list/get/ratify/stop
  shared/api/client.ts              # fetch wrapper + SERVICE_STOPPED interceptor
  shared/auth/ServiceStoppedNotice.tsx
  routes.tsx                        # new routes + role guards
```

The repo has no code yet, so this layout is a proposal. Adjust it to whatever the web-ui scaffold settles on.

### 1. Application form (`/register`)
- Fields: hospital name, registration/licence number, address (multi-line), contact person, phone,
  email (labelled "your admin login"), password, bed count, certificate upload. **All are mandatory.**
- Client-side validation, matching the server:
  - Every field required, with an error shown next to each empty field (AC-1)
  - Email format; bed count must be a positive integer
  - Certificate required; type must be `application/pdf`, `image/jpeg` or `image/png`; size ≤ 10 MB (AC-2)
- Upload field: drag-and-drop plus "Browse"; show the selected file's name and size with a remove button.
- On submit: disable the button, then send the multipart `POST`. Map a `400` response's `errors`
  onto the fields, and show a `409` as a form-level message.
- On `201`: call `POST /api/auth/login` with the submitted credentials, store the session the way
  the app's auth layer does, then navigate to `/onboarding/welcome`.

### 2. Post-submit page (`/onboarding/welcome`)
- Banner: "Application received — Temporarily Approved", with copy explaining that access is full
  now and the certificate will be verified.
- Read-only stepper: Departments → Packages → Rate lists & branding → Go live.
- "Start setup" button navigates to the Departments step route (a placeholder until that work
  item exists).
- No features are disabled while temporarily approved (FR-4).

### 3. Super Admin review queue
- Table columns: Hospital, Reg. no., Beds, Applied date, Status, Actions (View / Ratify / Stop service).
- Status filter, defaulting to `temporarily_approved`.
- Detail page: every application field, a certificate preview (inline `<iframe>`/`<img>` from
  `certificateUrl`, plus a download link), and the review history (action, Super Admin, timestamp).
- **Ratify:** a single confirm, then call the endpoint and update the row.
- **Stop service:** a confirm dialog that says "All access for this hospital will be blocked.",
  then call the endpoint.
- Hide both actions once the application is no longer `temporarily_approved`.
- Route guard: only the Super Admin role can open these routes.

### 4. Service stopped handling
- Login page: when login returns `403 SERVICE_STOPPED`, show the ServiceStoppedNotice
  ("This hospital's access has been stopped by the platform administrator.") instead of the
  generic error.
- Global interceptor in `shared/api/client.ts`: any authenticated response with `SERVICE_STOPPED`
  clears the session and redirects to `/login?reason=service_stopped`, which shows the same notice
  (AC-7).

## Testing
- Component tests for the form's validation: each empty field, email, bed count, and file type
  and size (AC-1, AC-2).
- Submit flow with a mocked API: `201` leads to login and then `/onboarding/welcome`; `400` shows
  errors on the fields; `409` shows the form-level message.
- Queue and detail pages: list rendering, filter, Ratify and Stop service with confirmation, and
  actions hidden after a decision.
- Interceptor: a `SERVICE_STOPPED` response on any request leads to logout and the notice.

## Assumptions & risks
- **Contract drift:** the endpoints above are proposed here. Finalise them together with the API
  work item's `impl.md`, which is still waiting on answers about tenant isolation.
- **Password rules** (length and complexity) aren't specified. Use the Identity Service's policy
  once it's defined, and until then require at least 8 characters.
- **Libraries** (router, form/validation, test runner) aren't chosen yet. Use whatever the web-ui
  scaffold standardises on (e.g. React Router, React Hook Form + Zod, Vitest + Testing Library).
- **Certificate preview** depends on the API returning a signed URL that the browser can render
  inline. PDFs may need `Content-Disposition: inline`.
