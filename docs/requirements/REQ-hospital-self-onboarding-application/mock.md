# Mock — Hospital Self-Onboarding Application

## 1. Application form

```
┌──────────────────────────────────────────────────────────────┐
│  Register your hospital                                      │
├──────────────────────────────────────────────────────────────┤
│  Hospital name *               [______________________________]│
│  Registration / licence no. *  [______________________________]│
│  Address *                     [______________________________]│
│                                [______________________________]│
│  Contact person *              [______________________________]│
│  Phone *                       [______________________________]│
│  Email * (your admin login)    [______________________________]│
│  Password *                    [______________________________]│
│  Bed count *                   [_____]                         │
│                                                              │
│  Clinical Establishment registration certificate *           │
│  ┌────────────────────────────────────────────────────────┐  │
│  │  Drag & drop or [Browse]   PDF / JPG / PNG, max 10 MB  │  │
│  └────────────────────────────────────────────────────────┘  │
│                                                              │
│  * all fields are mandatory              [ Submit application ]│
└──────────────────────────────────────────────────────────────┘
```

## 2. After submission (temporary approval granted)

```
┌──────────────────────────────────────────────────────────────┐
│  ✔ Application received — Temporarily Approved               │
│                                                              │
│  You have full access now. Our team will verify your         │
│  certificate; you'll be notified of the outcome.             │
│                                                              │
│  Next: set up your hospital                                  │
│   [1] Departments → [2] Packages → [3] Rate lists & branding │
│   → [4] Go live                                              │
│                                          [ Start setup → ]   │
└──────────────────────────────────────────────────────────────┘
```

## 3. Super Admin review queue

```
┌────────────────────────────────────────────────────────────────────────┐
│  Hospital applications                     Filter: [Temporarily Approved▾]│
├──────────────────┬───────────────┬────────┬────────────┬───────────────┤
│ Hospital         │ Reg. no.      │ Beds   │ Applied    │ Actions       │
├──────────────────┼───────────────┼────────┼────────────┼───────────────┤
│ Sunrise Maternity│ CE/2026/0412  │ 40     │ 2026-09-25 │ [View] [Ratify] [Stop service] │
│ City Care Nursing│ CE/2026/0398  │ 25     │ 2026-09-24 │ [View] [Ratify] [Stop service] │
└──────────────────┴───────────────┴────────┴────────────┴───────────────┘

  [View] → application details + certificate preview
  [Stop service] → confirm dialog: "All access for this hospital will be blocked."
```

## 4. Sign-in after service stopped

```
┌──────────────────────────────────────────────┐
│  ✖ Service stopped                           │
│  This hospital's access has been stopped by  │
│  the platform administrator.                 │
└──────────────────────────────────────────────┘
```
