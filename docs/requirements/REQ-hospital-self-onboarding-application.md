# REQ-hospital-self-onboarding-application — Hospital Self-Onboarding Application

- **Feature:** feat-hospital-self-onboarding — Hospital Self-Onboarding
- **Decision:** dec-go-5
- **Project:** prj-maternity-hospital-31
- **Blueprint reference:** Hospital Onboarding & Departments → Registration Flow; Tenant Provisioning service

## Summary

A hospital can apply to the platform on its own. It submits an application form, uploads its
Clinical Establishment registration certificate, and receives **temporary approval automatically**
on submission. A Super Admin later reviews the application offline and either **ratifies** the
hospital or **stops the service**. As part of onboarding, the hospital's **250k revenue milestone**
is initialised.

## Actors

| Actor | Role in this requirement |
|---|---|
| Applicant (becomes Hospital Admin) | Fills in the application, uploads the certificate, creates the Hospital Admin login |
| Super Admin | Reviews applications offline; ratifies or stops service |
| Tenant Provisioning service | Creates the tenant, grants temporary approval, tracks status |
| Document Service / AWS S3 | Stores the uploaded certificate |

## Functional requirements

### FR-1 Application form
The applicant must provide all of the following. **Every field is mandatory**; the form cannot be
submitted while any field is empty.

| Field | Notes |
|---|---|
| Hospital name | |
| Registration / licence number | As printed on the Clinical Establishment registration certificate |
| Address | |
| Contact person | Name of the person applying |
| Phone | |
| Email | Also used as the Hospital Admin login (see FR-3) |
| Bed count | Positive integer |

### FR-2 Proof of existence
- The only accepted document is the **Clinical Establishment registration certificate**.
- Exactly one certificate is required to submit.
- Accepted file types: PDF, JPG, PNG. Maximum size: 10 MB.
- The file is stored in AWS S3 through the Document Service and linked to the hospital's tenant record.

### FR-3 Hospital Admin account (self sign-up)
- The applicant creates the Hospital Admin login (email + password) as part of the application.
  No Super Admin action is needed to create the account.
- The account can sign in as soon as approval is granted (FR-4).
  Afterwards, the Hospital Admin creates all other hospital users (Super Admin → Hospital Admin →
  other users chain, per the blueprint).

### FR-4 Temporary approval
- When the application is submitted with a valid certificate, the hospital is set to
  **Temporarily Approved** automatically and immediately.
- **No restrictions** apply while temporarily approved. The Hospital Admin can do everything a
  ratified hospital can: continue the onboarding workflow (departments, packages, rate lists,
  header/footer branding), create staff and register patients.
- After submission, the applicant lands on the start of the onboarding workflow
  (Decide Departments → Decide Packages → Configure Rate Lists + Branding → Go Live).

### FR-5 Super Admin review
- Super Admin sees a queue of temporarily approved hospitals, with the application details and the
  uploaded certificate.
- Super Admin can take one of two actions:
  - **Ratify:** status becomes **Ratified — Full Service**. Nothing changes for the hospital's users.
  - **Stop service:** status becomes **Service Stopped**. **Nothing in the tenant can be accessed**
    by any hospital user (Hospital Admin, staff or patients). Sign-in shows a "service stopped"
    message.
- Every review action is recorded with the Super Admin's id and a timestamp.

### FR-6 250k revenue milestone initialisation
- When the tenant is created, a revenue-milestone record is initialised for the hospital with a
  threshold of **250,000** and a running revenue total of **0**.
- The hospital's revenue is accumulated against this milestone from then on.
- What happens when a hospital reaches the milestone is not defined by this requirement (see Open items).

## Status lifecycle

```
Applied ──(certificate uploaded, auto)──> Temporarily Approved ──(Super Admin)──> Ratified — Full Service
                                                   │
                                                   └──(Super Admin)──> Service Stopped
```

## Acceptance criteria

1. Submitting with any mandatory field empty is blocked, and each empty field is shown as an error.
2. Submitting without a certificate, or with a file that isn't PDF/JPG/PNG or is over 10 MB, is blocked.
3. A valid submission creates the tenant and the Hospital Admin account, stores the certificate in S3,
   and sets status to Temporarily Approved without manual intervention.
4. Immediately after submission, the Hospital Admin can sign in and use every feature without restriction.
5. The new hospital appears in the Super Admin review queue with its details and certificate.
6. Ratify changes status to Ratified with no loss of access or data.
7. Stop service changes status to Service Stopped. After that, no hospital user can access anything in the tenant.
8. A new tenant has a revenue-milestone record with threshold 250,000 and total 0.

## Open items

- **Behaviour on reaching 250k:** the milestone is initialised and tracked here, but the blueprint
  still lists "after 250k what all will happen" as unresolved. That needs its own decision.
- **Currency and definition of revenue** for the 250k milestone (e.g. INR, and which transactions count)
  still need to be confirmed.
