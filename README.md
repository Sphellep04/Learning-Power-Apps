# Learning-Power-Apps
This will be my masterplan to learn Power Platforms.

# Power Platform Mastery Plan
### For Phellep Shapopi, Developer Trainee at Salt Essential IT

---

## 1. Is the project idea right?

Yes, keep it. The **Employee Asset + Leave Tracker** is a good choice for one main reason: it forces you to touch every part of the platform inside one coherent story, rather than five disconnected demo apps. That matters because in real client work at Salt Essential IT, you'll rarely build "just a Power App" or "just a flow." You'll build a small system where the pieces talk to each other, and that's exactly what this project simulates.

A few adjustments worth making to the original brief:

- **Keep it single-tenant while learning.** The idea of expanding it into a multi-company product is good long term, but trying to build multi-tenancy on day one will slow down the actual learning. Master the single-company version first, then revisit multi-tenancy in week 7 or 8 once the fundamentals are solid.
- **Treat it as a client engagement, not a tutorial.** Write a one-page "requirements doc" before you start (who are the users, what do they need, what does success look like). This is a habit Salt Essential will expect from you anyway, and it makes the project far more convincing on a CV.
- **Design for real Namibian business use from day one.** Small tweaks, like using NAD for any costs, local public holiday logic for leave calculations, and mobile-data-conscious app design, will make the finished project a genuinely useful thing to show a client, not just a training exercise.

---

## 2. The realistic plan

The original 20-day, 45 to 60 minute plan is a reasonable taster but it compresses too much into too little time to actually stick. Given that you're now working with Power Platform daily at Salt Essential, I'd restructure this as an **8-week plan**, with two modes of learning running in parallel:

- **Structured study time**: roughly 5 to 7 hours a week, outside of work, focused on the project below and on Microsoft Learn's PL-900/PL-400 material.
- **On-the-job absorption**: pay deliberate attention to how real client apps at Salt Essential are structured, and ask to shadow or take small pieces of live work as soon as you're comfortable. Nothing accelerates mastery like seeing how a messy real requirement gets solved.

Each week below has a goal, concrete tasks, and a "you'll know you've got it when" checkpoint, so you can self-assess rather than just ticking off tasks.

### Week 1: Foundations, data modelling
**Goal:** Understand where data lives and why.

- Set up your free Power Apps Developer Plan environment (do this on day one, it's free and gives you a proper environment to break things in without risk).
- Build the data model twice: once in Dataverse (tables: Assets, LeaveRequests, Employees, with proper relationships and choice columns) and once as SharePoint Lists with the same fields.
- Deliberately compare the two. Try a lookup/relationship in both and notice how much more natural relational data feels in Dataverse versus SharePoint's lookup columns.

**Checkpoint:** You should be able to explain, in your own words and without notes, when you'd choose SharePoint over Dataverse for a client, and why. If you can't, you're not ready to move on.

### Week 2: Canvas apps
**Goal:** Build a working front end, and understand Power Fx properly rather than copying formulas.

- Build the Employee Self-Service Canvas app: a gallery of "My Assets" filtered to the logged-in user, and a leave request form using `Patch()`.
- Deliberately write the same screen two ways: once using `SubmitForm()` with a proper Edit Form control, once using `Patch()` directly. Understand the trade-offs of each (Patch gives more control but you own validation yourself).
- Break delegation on purpose. Filter a large list using a non-delegable function like `Left()`, watch the warning appear, then fix it. Understanding delegation by breaking it sticks far better than reading about it.

**Checkpoint:** You can explain what a delegation warning means to someone non-technical, and you know at least three functions that don't delegate against SharePoint.

### Week 3: Model-driven apps
**Goal:** Understand the "data first" mental model, and when it beats Canvas.

- Build the Admin app as a Model-driven app over your Dataverse tables from week 1.
- Add at least one Business Rule (no-code logic) and one View filtered to a specific condition (for example, "Leave requests pending more than 3 days").
- Set up basic Security Roles and test what happens when you view the app as a restricted user versus an admin.

**Checkpoint:** You can list three scenarios where you'd default to Model-driven over Canvas for a client, without hesitating.

### Week 4: Automation
**Goal:** Make the system reactive, not just a static form.

- Build the approval flow: new leave request triggers a Teams Adaptive Card approval to a manager.
- Build a notification flow: asset assigned triggers an employee email.
- Build a scheduled flow: check for assets nearing warranty expiry and alert an admin.
- Deliberately add error handling using Scope and Configure Run After, even though it feels like unnecessary ceremony at this stage. It won't feel unnecessary the first time a flow fails silently in production.

**Checkpoint:** You can explain the difference between an automated flow, an instant flow, and a scheduled flow, and which licence context each one runs under (this matters for the licensing section below).

### Week 5: Reporting
**Goal:** Turn raw data into something a manager would actually look at.

- Connect Power BI Desktop to your SharePoint/Dataverse data, and clean it properly in Power Query rather than fixing things downstream in DAX.
- Build three visuals minimum: leave by department, assets by status, and average approval time (this last one will force you to learn basic DAX, such as `CALCULATE` and `DATEDIFF`).
- Publish to the Power BI Service and set up Row Level Security so a manager only sees their own department.

**Checkpoint:** You can explain Row Level Security to a non-technical stakeholder and demonstrate it actually restricting data, not just describe it in theory.

### Week 6: Conversational AI
**Goal:** Add a natural language layer over the same data.

- Build a simple HR bot in Copilot Studio that can answer "how many leave days do I have left" by querying your data source.
- Connect it to a Power Automate flow rather than hardcoding logic in the bot itself, since that's the pattern you'll use at work.

**Checkpoint:** You understand the difference between an authored Topic and a Generative Answer in Copilot Studio, and when each is appropriate.

### Week 7: Making it production-grade
**Goal:** This is the week that separates a training project from something you'd actually hand to a client, and it's the most commercially valuable week of the whole plan.

- Move everything into a proper Solution (never build directly in the Default Solution again after this week).
- Set up Dev, Test, and Prod environments and practise moving your Solution between them.
- Tighten security: proper Entra ID groups, field-level security where relevant, and a genuine test with two different user roles.
- Fix performance: add loading spinners, use `Concurrent()` where you have independent data calls, and run the Monitor tool to actually see where time is being spent, rather than guessing.

**Checkpoint:** You could hand this Solution to a colleague and they could deploy it to a fresh environment using your documentation alone.

### Week 8: Polish and presentation
**Goal:** Package it so it's genuinely usable in a CV, portfolio, or interview.

- Record a 5-minute walkthrough demo.
- Write a short case study: the business problem, what you built, what you'd do differently with more time.
- Optional stretch: revisit the multi-tenant idea now that the fundamentals are solid, and sketch (doesn't need to be built) how you'd adapt the data model for multiple companies.

---

## 3. Full project breakdown: Employee Asset + Leave Tracker

Here's the detail behind the plan above, so you understand not just what to build but why each piece exists.

### Data model
| Table | Key fields | Notes |
|---|---|---|
| Employees | Name, Email, Department, Manager (lookup to self) | The Manager self-lookup is a good early lesson in self-referencing relationships |
| Assets | Asset Tag, Type, AssignedTo (lookup), Status, WarrantyExpiry | Status as a Choice column, not free text, so PowerBI can group cleanly |
| LeaveRequests | Employee (lookup), StartDate, EndDate, Status, ApprovedBy | Status choices: Pending, Approved, Rejected |

### Apps
- **Employee app (Canvas):** view my assets, request leave, see my leave balance and history. Mobile-first, since field and warehouse staff would use this on a phone.
- **Admin app (Model-driven):** manage all assets and leave records, run the approval Business Process Flow (Draft to Manager Approval to HR Approval to Done).

### Automation (Power Automate)
1. Leave request created to Teams approval card to manager.
2. Asset assigned to employee email notification.
3. Scheduled check, three days before warranty expiry, to admin alert.
4. Leave approved to Outlook calendar event created via the calendar connector.

### Reporting (Power BI)
- Leave by department (bar chart).
- Assets by status (donut or stacked bar, donut only if you have three or fewer categories, otherwise it gets hard to read).
- Average approval time (a DAX measure using `DATEDIFF` between request created and approved date, wrapped in `AVERAGEX`).

### Conversational layer (Copilot Studio)
- "How many leave days do I have left" (calls a Power Automate flow that queries LeaveRequests and returns a calculated balance).
- "What assets am I assigned" (same pattern, different query).

### Example Power Fx you'll actually use
```
// Show only my assets
Filter(Assets, AssignedTo.Email = User().Email)

// Colour code leave status
If(ThisItem.Status = "Approved", Green,
   ThisItem.Status = "Rejected", Red,
   Orange)

// Save a new leave request
Patch(LeaveRequests, Defaults(LeaveRequests),
    {
        Employee: LookUp(Employees, Email = User().Email),
        StartDate: DatePickerStart.SelectedDate,
        EndDate: DatePickerEnd.SelectedDate,
        Status: "Pending"
    }
)
```

---

## 4. Licensing, properly explained

This is worth understanding thoroughly, because Microsoft changed the Power Apps licensing structure in January 2026, and a lot of material still online (including the plan you were sent) is out of date on this point.

### What you need for learning (right now, this project)
The **Power Apps Developer Plan** is free, and is genuinely enough for everything in this plan. It gives you a personal environment with Dataverse, Power Apps, and Power Automate, so you can build and test everything above without spending anything. The only limitation is that apps built here can't be shared with other users, since it's a solo, non-production environment. That's fine for a portfolio project.

### What a real client project needs (this is the part that matters at Salt Essential)

**Power Apps**
- **Premium licence** (formerly called "per user"): around $20/user/month list price, sometimes shown as $12/user/month at enterprise volume (2,000+ seats). One licence per user covers unlimited apps, Dataverse, and premium connectors. This is the plan most organisations land on once they have more than one or two apps.
- **Pay-as-you-go**: around $10 per unique active user per app per month, billed through an Azure subscription. You only pay for users who actually open the app that month, which suits pilots, seasonal use, or a single low-frequency app.
- **Per App plan**: worth knowing about because you'll see it mentioned everywhere, but Microsoft removed it from general sale in January 2026. It still exists for existing Enterprise Agreement customers and came back for CSP (Cloud Solution Provider) channel customers in April 2026, but new non-CSP customers generally can't buy it any more. If a client asks about it, the honest answer now is "Premium or Pay-as-you-go, unless you're already on an Enterprise Agreement."

**Power Automate**
- **Premium licence**: around $15/user/month, needed for premium connectors, RPA (desktop flows), and AI Builder.
- **Process licence**: licenses the flow itself rather than a user, useful for a single business-critical flow that many people trigger (an approval flow used company-wide, for instance). Comes with a much higher daily action allowance (250,000/day).
- Basic Microsoft 365 licences include limited Power Automate rights for standard connectors only. The moment a flow needs SQL, Dataverse beyond the free tier, or an HTTP/custom connector, you're into Premium territory.
- **Pay-as-you-go**: premium cloud flow runs are billed at roughly $0.60 per run through Azure, only when a flow uses premium connectors; standard-connector flows aren't metered this way.

**Power BI**
- **Desktop**: free, for building reports locally.
- **Pro**: around $10/user/month, needed to publish, share, and collaborate in the Power BI Service.
- **Premium Per User (PPU)**: around $20/user/month, adds larger dataset limits, more frequent refreshes, and advanced AI features.
- **Fabric capacity (organisation-wide Premium)**: starts from roughly $262/month for a capacity, this is a tenant-level purchase rather than per user, worth it once you have many report consumers rather than a handful.

**Copilot Studio**
- Licensed at the tenant level, starting around $200/month base, which includes a monthly message allowance (25,000 messages/month at the base tier).
- Beyond that, usage is billed in Copilot Credits, consumed per message, per autonomous trigger, and per 100 flow actions executed within an agent. Autonomous triggers (an agent acting on a schedule or event without a user asking it to) are billed even for users who already have a Microsoft 365 Copilot seat, so these can add up quietly if agents are left running unattended.
- A separate per-user Copilot Studio licence is needed for anyone authoring bots, on top of the tenant licence.

**Dataverse (the shared database underneath most of this)**
- Storage capacity is bundled into Premium Power Apps, Power Automate Premium/Process, and Copilot Studio licences, and pools at the tenant level.
- Extra storage is billed per GB/month once you exceed what's included, split across database, file, and log capacity.

### A simple way to think about it for Salt Essential client conversations
Ask three questions before recommending a licensing model to a client:
1. **How many distinct apps will each user need?** One infrequent app points to Pay-as-you-go. Several apps points to Premium.
2. **Do they need premium connectors (SQL, Salesforce, HTTP, custom connectors)?** If yes, Premium/Process licensing is unavoidable somewhere in the chain, standard Microsoft 365 entitlements won't cover it.
3. **Is this a pilot or a permanent system?** Pilots suit Pay-as-you-go, since there's no ongoing subscription commitment. Permanent systems with steady user counts are usually cheaper on Premium once you cross roughly two apps per user.

---

## 5. Free resources to use alongside this plan

- **Microsoft Learn**: search "PL-900" (fundamentals) then "PL-400" (developer) learning paths, both free.
- **YouTube**: Shane Young and April Dunnam are still the strongest independent Power Apps educators.
- **Practice data**: mockaroo.com to generate realistic fake employee and asset data.
- **Licensing reference**: Microsoft's own Power Platform licensing guide (updated periodically) is the only source guaranteed to be current, since third-party pricing articles go stale within months.

## 6. Tracking progress

At the end of each week, write two or three sentences answering: what did I build, what broke, and what would I do differently. This single habit, done consistently for eight weeks, will do more for your understanding than any additional hour of tutorial-watching. It's also exactly the kind of reflection that reads well if a senior developer at Salt Essential ever asks you to talk through your learning process.