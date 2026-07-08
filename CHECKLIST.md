## Progress Checklist

Related documents:
- [README.md](./README.md)
- [Power_Platform_Mastery_Plan.md](./Power_Platform_Mastery_Plan.md)
- [REQUIREMENTS.md](./REQUIREMENTS.md)
- [PROGRESS.md](./PROGRESS.md)

Tick these off as you go. GitHub renders these as clickable boxes directly in the README, and will show a small "x of y" count under the heading once you start ticking them.

### Week 1: Foundations, data modelling
- [ ] Set up free Power Apps Developer Plan environment
- [ ] Build Dataverse tables (Assets, LeaveRequests, Employees) with relationships and choice columns
- [ ] Build the same model as SharePoint Lists
- [ ] Compare the two, note when you'd choose each
- [ ] Checkpoint: can explain SharePoint vs Dataverse trade-offs without notes

### Week 2: Canvas apps
- [ ] Build Employee Self-Service app, gallery filtered to logged-in user
- [ ] Build leave request form using SubmitForm()
- [ ] Rebuild the same form using Patch() instead, compare the two
- [ ] Break a delegation warning on purpose, then fix it
- [ ] Checkpoint: can explain a delegation warning to a non-technical person

### Week 3: Model-driven apps
- [ ] Build the Admin app as Model-driven over the Dataverse tables
- [ ] Add at least one Business Rule
- [ ] Add a filtered View (e.g. leave pending more than 3 days)
- [ ] Set up Security Roles and test as a restricted user
- [ ] Checkpoint: can list three scenarios where Model-driven beats Canvas

### Week 4: Automation
- [ ] Flow 1: leave request created to Teams approval card
- [ ] Flow 2: asset assigned to employee email
- [ ] Flow 3: scheduled warranty expiry check to admin alert
- [ ] Add error handling (Scope, Configure Run After) to at least one flow
- [ ] Checkpoint: can explain automated vs instant vs scheduled flows and licence context

### Week 5: Reporting
- [ ] Connect Power BI Desktop to SharePoint/Dataverse data
- [ ] Clean data in Power Query
- [ ] Build: leave by department chart
- [ ] Build: assets by status chart
- [ ] Build: average approval time (DAX measure)
- [ ] Publish to Power BI Service and set up Row Level Security
- [ ] Checkpoint: can demonstrate RLS actually restricting data for a test user

### Week 6: Conversational AI
- [ ] Build HR bot in Copilot Studio
- [ ] Connect a "how many leave days do I have" topic to a Power Automate flow
- [ ] Add a second topic (e.g. "what assets am I assigned")
- [ ] Checkpoint: can explain authored Topics vs Generative Answers

### Week 7: Making it production-grade
- [ ] Move everything into a proper Solution
- [ ] Set up Dev, Test, and Prod environments
- [ ] Move the Solution between environments at least once
- [ ] Set up Entra ID groups and test with two different roles
- [ ] Add loading spinners and Concurrent() where relevant
- [ ] Run the Monitor tool and note any performance issues
- [ ] Checkpoint: a colleague could deploy this using your documentation alone

### Week 8: Polish and presentation
- [ ] Record a 5-minute demo walkthrough
- [ ] Write a short case study (problem, build, what you'd change)
- [ ] Optional: sketch a multi-tenant version of the data model
- [ ] Publish/update this repo as the final portfolio piece
