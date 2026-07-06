# Automation Design Exercise — 500 Inbound Leads/Day

**Task:** Design the step-by-step automation process for a company receiving 500 inbound leads daily, covering: capture → enrich → qualify → generate a personalized email → update CRM → notify the assigned SDR. Focus on the workflow, not the specific tools.

## My First Draft

1. User lands on the landing page; clicks are recorded, and the user navigates to the form.
2. The form captures Name, Company Email, Company Name, Full Name, Team Size, Designation, Product Interest, and Current Problem — plus hidden marketing fields (UTM parameters, device details).
3. The submission is stored as a Contact in HubSpot (or an equivalent CRM).
4. Clay/Apollo enriches each contact's data and scores the lead for conversion potential (company size, funding, employee size, market value).
5. The enriched data is written back to the CRM against each contact.
6. An automation tool (e.g. n8n) notifies the Sales team with client name, lead score, company name, and preferred time to connect.
7. AI provides background info, lead score context, and company notes so Sales doesn't have to do manual research.
8. A fallback path exists: if enrichment produces bad/garbage data, it's flagged for Ops or Sales to review manually.

**Mentor rating:** 9.2/10 — "You're starting to think like a GTM Systems Engineer."

## Feedback & Production-Ready Improvements

| Area | Feedback | Fix |
|---|---|---|
| Traffic source | Where did the user come from *before* the landing page? | Track the channel (Google Ads / LinkedIn / Organic) for CAC, ROI, and attribution |
| Field naming | "Employee Name", "Company Mail" | Standardize to "Full Name", "Work Email" |
| CRM stage | Lifecycle stage wasn't set automatically | Auto-set `Lifecycle Stage = Lead` on creation, never manual |
| Enrichment | Missed buyer identification | Apollo/Clay should also surface decision-maker status (VP / Director / Manager / IC) |
| CRM updates | Risk of overwriting good data | Only update empty fields; never overwrite existing values |
| Notifications | Every lead notified the same way | Route by lead score: >80 → immediate Slack alert, 40–80 → queue, <40 → marketing nurture |
| AI output | Good, but could go further | Also generate: company summary, pain points, suggested demo focus, discovery questions |
| Failure handling | Jumped straight to manual review | Retry → retry again → secondary enrichment provider → *then* manual review + notify RevOps |
| **Missing: Qualification layer** | Not every lead should go to Sales | Score >80 → Sales; else → Marketing Nurture |
| **Missing: Duplicate detection** | Same lead submitting multiple times = multiple notifications | Check by email — update existing contact instead of creating a new one |
| **Missing: Lead routing** | "Notify Sales" is too generic | Round-robin / rule-based assignment (by region, segment, or deal size) to a specific rep |

## Final Production-Ready Workflow

```
Landing Page
  → Lead Form
  → Duplicate Check
  → Create/Update Contact
  → HubSpot
  → n8n Trigger
  → Clay Enrichment
  → Lead Scoring
  → Qualification (Sales / Marketing Nurture)
  → AI Summary
  → Assign Sales Rep (Round Robin)
  → Slack Notification
  → Personalized Email (OpenAI)
  → CRM Updated
  → Meeting Booking
  → Customer
```

## Interview-Style Questions to Keep in Mind

- What happens if Apollo/Clay is down for two hours?
- How do you prevent duplicate leads?
- Why should every lead go to Sales — should it?
- How would you reduce API costs at 50,000 leads/month?

## Six Questions to Ask for Every Workflow

1. Where does the data come from?
2. Where is it stored?
3. Who needs it?
4. What decisions are made automatically?
5. What happens if something fails?
6. How can we reduce manual effort?
