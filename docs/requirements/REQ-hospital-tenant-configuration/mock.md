# Mock: Hospital Tenant Configuration

```
+--------------------------------------------------------------+
| [Logo]  Hospital Name                    Admin: Dr. Rao  [v] |
+--------------------------------------------------------------+
| Configuration                                                |
| +-------------+ +------------------------------------------+ |
| | Departments | | Rate Lists                               | |
| | Packages    | |                                          | |
| |>Rate Lists  | |  List: [ Rate List 1 v ]                 | |
| | Staff       | |                                          | |
| | Branding    | |  Category            Rate      Cap       | |
| | Operational | |  Normal delivery    [ 12000 ] [ 15000 ]  | |
| |   Settings  | |  LSCS               [ 30000 ] [ 35000 ]  | |
| |             | |  Forced             [ ..... ] [ ..... ]  | |
| |             | |  NICU               [ ..... ] [ ..... ]  | |
| |             | |                                          | |
| |             | |  Changes take effect immediately.        | |
| |             | |             [ Cancel ]  [ Save ]         | |
| +-------------+ +------------------------------------------+ |
+--------------------------------------------------------------+

Staff:              [+ Add user]  Name | Email | Role v | [Deactivate]
Branding:           Name [____]  Logo [Upload]  Colours [__][__]
                    Receipt header [____]  Receipt footer [____]
Operational:        Working hours  Mon-Sat [09:00] to [17:00]

Tenant not yet active:  banner "Configuration unlocks after Super
                        Admin ratifies your onboarding"; fields disabled.
```
