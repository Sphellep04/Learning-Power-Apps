# Requirements Document
### Employee Asset + Leave Tracker

Related documents:
- [README.md](./README.md)
- [Power_Platform_Mastery_Plan.md](./Power_Platform_Mastery_Plan.md)
- [CHECKLIST.md](./CHECKLIST.md)
- [PROGRESS.md](./PROGRESS.md)

**Prepared by:** Phellep Shapopi
**Version:** 1.0
**Status:** Draft, for the learning project described in Power_Platform_Mastery_Plan.md

Note: this document reflects Namibia's Labour Act, 2007 as a realistic basis for the leave logic, so the project behaves like something a real Namibian employer could use, not a generic template. It is written for learning purposes and is not legal advice. Any organisation actually adopting this system should confirm current entitlements with the Ministry of Labour, Industrial Relations and Employment Creation, or with their own HR and legal advisors, since leave policies can also be more generous than the statutory minimum.

---

## 1. Background and purpose

Small and medium Namibian businesses (10 to 150 staff is a realistic target range) commonly manage staff leave and company assets through a mix of WhatsApp messages, paper forms, and Excel sheets kept by an office administrator. This creates three recurring problems that this system is meant to solve:

- Leave balances are calculated by hand and are often wrong, since the sick leave cycle runs over 36 months rather than annually, which is easy to get wrong manually.
- Assets (laptops, phones, tools, vehicles) get assigned informally, and there's often no clear record of who currently holds what when someone leaves the company.
- Managers have no visibility of team leave patterns or asset status without asking the administrator directly.

The system replaces this with a small, self-service platform where employees can check their own leave and assets, managers can approve requests from their phone, and HR/admin has one accurate source of truth.

## 2. Stakeholders

| Role | Who they are | What they need |
|---|---|---|
| Employee | General staff, often on a mobile device with limited data | See leave balance, request leave, see assets assigned to them |
| Line manager | Department head | Approve or reject leave requests, see team leave calendar |
| HR/Admin | Office administrator or HR officer | Manage all leave records and assets, generate reports, onboard/offboard staff |
| Executive/Owner | Managing director or similar, in a small business this is often the same person approving everything | High-level dashboard, department comparisons, cost visibility |

## 3. Scope

### In scope
- Leave request and approval workflow, calculated against Namibia's actual statutory entitlements.
- Asset register with assignment history and condition tracking.
- Manager dashboard and reporting.
- A simple chatbot for common questions ("how many leave days do I have").

### Out of scope for this version
- Payroll integration or salary calculations.
- Full HR functions such as recruitment, performance reviews, or disciplinary records.
- Multi-company/multi-tenant support (noted as a possible Phase 2 in the mastery plan, week 8).
- Integration with the Social Security Commission systems for maternity and sickness benefit claims. This stays a manual, offline process for now, the system only tracks that leave was taken.

## 4. Functional requirements

### 4.1 Employee records
- FR1.1: Each employee has a record with name, department, manager, employment start date, and ordinary working days per week (5 or 6), since this single field drives both annual leave and sick leave calculations.
- FR1.2: The system must distinguish between permanent and probationary staff, since sick leave accrues differently in the first year of employment (one day accrued for every 26 days worked) compared to after the first year.

### 4.2 Leave management
- FR2.1: Annual leave entitlement is calculated as ordinary working days per week multiplied by four. A 5-day week gives 20 working days a year; a 6-day week gives 24 working days a year. This must be a calculated field, not manually entered, so it can never silently drift out of line with the law.
- FR2.2: Sick leave is tracked on a rolling 36-month cycle, not a calendar year. A 5-day-week employee is entitled to 30 working days of sick leave per cycle, and a 6-day-week employee to 36 working days. The system should flag to HR when an employee's current sick leave cycle is due to reset.
- FR2.3: The system should flag when a sick leave request exceeds 2 consecutive days, since a medical certificate is required at that point under the Labour Act, and prompt for one to be attached (a simple file upload is enough for this project, it doesn't need OCR or validation of the certificate itself).
- FR2.4: Public holidays must be excluded from leave-day calculations automatically. Namibia currently observes 13 public holidays a year (New Year's Day, Good Friday, Easter Monday, Independence Day, Workers' Day, Cassinga Day, Genocide Remembrance Day, Ascension Day, Africa Day, Heroes Day, Namibian Women's/Human Rights Day, Christmas Day, Family Day). Store these as a lookup table by year rather than hardcoding dates, since several move annually (Easter-linked dates) and government sometimes gazettes additional ones.
- FR2.5: Maternity leave requests should be recorded as a distinct leave type (12 weeks total, split 4 weeks before and 8 weeks after the expected date), separate from annual and sick leave, since it doesn't draw down against either.
- FR2.6: A leave request moves through Draft, Pending Manager Approval, Approved, and Rejected states. A rejected request should require a reason, since this is both good practice and something Namibian labour disputes commonly hinge on.

### 4.3 Asset management
- FR3.1: Each asset record includes tag number, type, purchase date, current holder, status (In Use, In Storage, Under Repair, Retired), and estimated value in NAD, since this is the currency the business actually budgets in.
- FR3.2: When an employee's employment ends, the system should flag any assets still assigned to them, so HR can't accidentally close out an offboarding without recovering company property. This is a genuinely common gap in small Namibian businesses that don't have a formal IT asset function.
- FR3.3: Asset reassignment must keep a full history, not just the current holder, since disputes about "who had the laptop when it broke" are common enough to design around from the start.

### 4.4 Notifications and approvals
- FR4.1: Managers receive an approval request through Microsoft Teams where available, but the design should assume this may not always be the case. Not every small Namibian business has Teams rolled out to all staff, so an email fallback should also exist for the approval notification.
- FR4.2: Employees receive confirmation once their request is approved or rejected.
- FR4.3: HR/Admin receives an alert when an asset's warranty is approaching expiry, or when it hasn't been reviewed (condition-checked) in over 12 months.

### 4.5 Reporting
- FR5.1: A dashboard shows leave taken by department, filterable by leave type (annual, sick, maternity).
- FR5.2: A dashboard shows assets by status and by estimated value, useful for budget conversations with the owner or executive.
- FR5.3: An average approval time metric, so HR can see whether managers are responding to requests promptly.
- FR5.4: Row Level Security ensures a manager only sees their own department's data by default, and only Executive/HR roles see company-wide figures.

### 4.6 Conversational layer
- FR6.1: An employee can ask "how many leave days do I have left" and get an answer reflecting both annual and sick leave balances, calculated correctly against the two different cycle types described above.
- FR6.2: An employee can ask what assets are currently assigned to them.

## 5. Non-functional requirements

- NFR1: **Mobile data awareness.** A meaningful share of users in Namibia access services on mobile data rather than Wi-Fi, and data costs are a genuine consideration for many staff. The Canvas app should avoid loading large images or unnecessary data on first load, and should function acceptably on a modest Android device, since this is the most common device type in the local market.
- NFR2: **Low connectivity tolerance.** Areas outside Windhoek and other major towns can have inconsistent connectivity. The app should show clear loading and error states rather than failing silently, so a user on a weak connection understands what's happening.
- NFR3: **Currency and localisation.** All monetary values are shown in NAD. Dates are shown in day/month/year format, matching local convention, not the US month/day/year default some controls use out of the box.
- NFR4: **Data protection.** Namibia does not yet have a fully enacted data protection act in force at the time of writing, though a bill has been in development. Given that, the system should still be built as if strong data protection applied, since this is good practice regardless and positions the project well for whenever local legislation does come into force. This means role-based access (Row Level Security, Security Roles) and no storing of sensitive personal data beyond what's operationally needed.
- NFR5: **Usability for non-technical users.** HR/Admin users are often not technical. Forms should use plain language, sensible defaults, and inline validation rather than relying on users reading error messages after submission.
- NFR6: **Auditability.** Every leave approval or rejection, and every asset reassignment, should be traceable to a user and timestamp, since these are the two areas most likely to end up in a workplace dispute.

## 6. Assumptions and constraints

- The organisation has at least basic Microsoft 365 licensing already, and the leave/asset system builds on top of that rather than replacing existing email or Teams.
- The business operates a standard 5-day or 6-day working week per employee; genuinely irregular schedules (casual or shift work with variable days) are out of scope for version 1, since the Labour Act calculates their entitlement differently again.
- This version assumes a single Namibian entity. A group of companies or cross-border staff would need the multi-tenant redesign mentioned in scope, section 3.
- Public holiday dates must be reviewed and updated annually, since several are date-linked to Easter and the list can change by government gazette.

## 7. Success criteria

The project is considered successful for learning purposes when:

- An employee can request leave and see an accurate, correctly calculated balance without HR needing to check it by hand.
- A manager can approve a request from their phone within the flow, without needing to open a separate admin tool.
- HR can generate a leave and asset report for a given month in under a minute, where previously this would have taken manual compilation from paper or spreadsheets.
- The system correctly refuses to let sick leave calculations reset annually, proving the 36-month cycle logic actually works rather than being approximated.

## 8. Open questions for a real deployment

These are worth thinking through even though they're not being built in this version, since a client will ask them:

- Should the system integrate with the Social Security Commission's maternity and sickness benefit process, or remain purely an internal record?
- Does the business want leave carried over between cycles, and if so, under what forfeiture rules? (The Labour Act allows leave to be taken up to four months after the cycle ends, extendable to six months with written agreement, and does not allow employers to pay out leave in place of it being taken, except on termination.)
- What happens to asset records when an employee moves between departments rather than leaving entirely?
