# ai-intern-assignment-priyanshu
# Student Lead Capture & Automation Assignment

**Name:** Priyanshu Raj

**Email:** contact.yespriyanshu@gmail.com

**Date of Submission:** 18/04/2026

---

## Part A — Student Lead Capture Form

A single-page HTML form built with pure HTML, CSS, and vanilla JavaScript — no frameworks used.

**How to run:**  
Open `index.html` directly in any browser. No server needed.

---

## Part B — N8N Automation Workflows

### B1 — Lead Notification Workflow (`workflow-b1-lead-notification.json`)

Triggered by a Webhook (POST). A Set node extracts and renames the incoming fields. An IF node checks the course level — if PG or PhD, an email/Slack notification (Gmail) is sent; otherwise the lead is logged to Google Sheets.

**How to import:**
1. Open N8N → New Workflow → Import from file
2. Select `workflow-b1-lead-notification.json`
3. Add your credentials (see placeholder values below)
4. Activate the workflow

**How to test:**  
POST the following JSON to your webhook URL using Postman or the browser console:
```json
{
  "fullName": "Test User",
  "email": "test@example.com",
  "country": "India",
  "courseLevel": "PhD",
  "preferredUniversity": "University of Oxford",
  "message": "I am interested in applying for a PhD programme."
}
```

---

### B2 — Scheduled Data Fetch Workflow (`workflow-b2-scheduled-fetch.json`)

Runs every day at 9:00 AM via a Cron/Schedule trigger. Fetches university data from a free public API, filters and transforms it using a JavaScript Code node, and outputs clean labelled records via a Set node.

**API Used: Hipolabs Universities API**  
`http://universities.hipolabs.com/search?country=United+Kingdom`

**Why this API?** It is completely free, requires no API key or sign-up, and returns structured JSON (university name, country, website, domain) that is directly relevant to the project. It also made sense to use it alongside the lead form — the same data could be used to validate or auto-suggest university names.

**How to import:**
1. Open N8N → New Workflow → Import from file
2. Select `workflow-b2-scheduled-fetch.json`
3. No credentials needed — the API is public
4. To test immediately: open the Schedule Trigger node and click "Execute Node"

---

## Part C — Form Connected to N8N Webhook

The HTML form from Part A was updated to POST data directly to the B1 webhook using `fetch()`.

- On submit, the button shows a `"Sending..."` loading state and is disabled
- On success, the thank-you screen is shown without a page reload
- On failure (network error or bad response), an error banner appears and the button is re-enabled so the user can retry

**Webhook URL used:**  
`https://raj61.app.n8n.cloud/webhook-test/student-lead (my n8n Link)`

> Note: The `/webhook-test/` URL only works when the workflow is open in N8N and listening for a test event.

**Screen Recording:** https://youtu.be/JBDwfekBaxU

---

## N8N Credentials & Placeholder Values

| Variable | Used In | Placeholder |
|---|---|---|
| Gmail ID | B1 - Gmail - Send a message | connect via your Gmail ID |
| Google Sheet ID | B1 — Google Sheets node | `put your sheet ID here` |
| Google Account | B1 — Google Sheets node | Connect via N8N OAuth credentials |

---

## Challenges & How They Were Resolved

The trickiest part was handling the N8N webhook response in Part C — the test webhook URL only responds when the workflow is actively listening in N8N, which caused the fetch() call to hang or throw a network error during testing. This was resolved by wrapping the fetch in a `try/catch` block so the form gracefully shows an error banner instead of silently failing. A second challenge was the conditional branching in B1: N8N's IF node needed a regex condition (`^(PG|PhD)$`) rather than two separate OR conditions to correctly match both values in one check.

---
