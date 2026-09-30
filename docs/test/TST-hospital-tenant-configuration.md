# TST-hospital-tenant-configuration: Hospital Tenant Configuration

- **Requirement:** [REQ-hospital-tenant-configuration](../requirements/REQ-hospital-tenant-configuration.md)
- **Work item:** none linked (no work items exist yet for this requirement)

## Test data
- Tenant A: active (onboarding ratified), with a Hospital Admin user.
- Tenant B: active, with its own Hospital Admin (for isolation checks).
- Tenant C: onboarded but **not** ratified (temporarily approved), with a Hospital Admin.
- A non-admin user in Tenant A for each role: USG Staff, Lady Doctor, LR Staff, Billing Reception, RMO, Incident Manager.

## Scenarios

### T1. Admin configures each area on an active tenant (AC-1)
| # | Steps | Expected |
|---|---|---|
| 1.1 | Sign in as Tenant A admin, open Departments, add a department, rename it, deactivate it | Each change saves and is shown on reload |
| 1.2 | Open Packages, create a package and edit it (including its cap) | Package saved with the new cap |
| 1.3 | Open Staff, create a user and assign a role from USG Staff, Lady Doctor, LR Staff, Billing Reception, RMO, Incident Manager | User is created and can sign in with the assigned role |
| 1.4 | Open Branding, set name, logo, colours, receipt header and footer | Values saved and shown in the app header and on generated receipts |
| 1.5 | Open Operational Settings, set working hours | Working hours saved and shown on reload |
| 1.6 | Open Rate Lists, edit rates on each of the 2 rate lists and the 4 delivery categories | Rates saved |

### T2. Rate list edits apply immediately (AC-3)
| # | Steps | Expected |
|---|---|---|
| 2.1 | Note the charge for a delivery category, edit its rate as admin, then immediately calculate a charge for a new patient in that category | The new charge uses the edited rate with no delay or publish step |
| 2.2 | Edit the rate again, then check the rate list | Only the latest value is shown; no version or effective-date fields exist |

### T3. Unratified tenant cannot edit (AC-2)
| # | Steps | Expected |
|---|---|---|
| 3.1 | Sign in as Tenant C admin and open configuration | Fields are disabled and a banner says configuration unlocks after Super Admin ratification |
| 3.2 | Call each configuration save endpoint directly as Tenant C admin | Every call is rejected and nothing is changed |
| 3.3 | Super Admin ratifies Tenant C, then the admin reloads | Configuration becomes editable |

### T4. Tenant isolation (AC-4)
| # | Steps | Expected |
|---|---|---|
| 4.1 | Tenant A admin edits a rate, department and branding | Tenant B's data is unchanged |
| 4.2 | Tenant A admin calls configuration endpoints using a Tenant B resource id | Request is rejected (not found or forbidden); no Tenant B data is returned or modified |
| 4.3 | Tenant A admin lists staff | Only Tenant A staff appear |

### T5. Non-admin roles cannot edit (AC-5)
| # | Steps | Expected |
|---|---|---|
| 5.1 | Sign in as each non-admin role and open the configuration route | Access is denied or the configuration menu is not shown |
| 5.2 | Call each configuration save endpoint directly as each non-admin role | Every call is rejected and nothing is changed |
| 5.3 | Call the same endpoints unauthenticated | Rejected as unauthenticated |

### T6. Validation and negative cases
| # | Steps | Expected |
|---|---|---|
| 6.1 | Save a rate or cap that is empty, negative or non-numeric | Rejected with a field-level error |
| 6.2 | Create a staff user with a duplicate email or no role | Rejected with a clear error |
| 6.3 | Save working hours where closing time is not after opening time | Rejected with a field-level error |
| 6.4 | Upload a non-image file as the logo | Rejected with an error |

## Out of scope
- Operational settings other than working hours.
- Any branding upload size or format limit beyond standard image validation (none specified in the requirement).
