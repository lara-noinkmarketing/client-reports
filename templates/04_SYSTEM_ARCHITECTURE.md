# System Architecture

## Overview

This is a **multi-client, template-based reporting system** designed to scale from 1 to 100+ real estate clients without touching existing workflows or creating new ones.

**Key Principle:** One parametrized workflow handles *all* clients. Configuration is data-driven, not code-driven.

---

## System Components

### 1. Master Clients Sheet (Google Sheets)

**Purpose:** Single source of truth for all client information

```
Columns:
- Client ID (unique identifier)
- Client Name
- Facebook Business ID
- Facebook Campaign ID (the one campaign per client)
- Contact Email
- Google Sheet ID (their tracker)
- GitHub Folder Name
- Status (active/inactive)
- Created Date
- Notes
```

**Example:**
```
| Client ID | Name | FB Business | FB Campaign | Email | Sheet ID | Folder | Status |
|-----------|------|-------------|-------------|-------|----------|--------|--------|
| 4305 | Real Estate 4305 | 123456 | campaign_1 | kent@... | sheet_1 | realestate4305 | active |
| 5000 | Bronwyn Sales | 654321 | campaign_2 | bron@... | sheet_2 | bronwyn | active |
| 5001 | Client Three | 789012 | campaign_3 | c3@... | sheet_3 | client-three | active |
```

**Who updates it:** Lara (when adding new clients) or Provisioning Workflow (automated)

---

### 2. Per-Client Google Sheets

**Purpose:** Track which ad groups to report on

**Columns:**
```
- Ad Group ID
- Ad Group Name (must follow: [property]--[suburb]--[goal])
- Property Name (extracted from ad group name)
- Suburb (extracted from ad group name)
- Goal (extracted from ad group name)
- Status (green = report, hold = skip, inactive = ignore)
- Last Report Sent (auto-updated by workflow)
- Notes
```

**Example:**
```
| Ad Group ID | Ad Group Name | Property | Suburb | Goal | Status | Last Report |
|-------------|---------------|----------|--------|------|--------|-------------|
| 52593... | 1-38-cooinda-st--eastern-heights--traffic | 1/38 Cooinda St | Eastern Heights | traffic | green | 2026-09-08 |
| 52594... | 47-white-gums--hattonvale--lead-generation | 47 White Gums Rd | Hattonvale | lead-gen | green | 2026-09-08 |
| 52595... | 64-grange-rd--eastern-heights--engagement | 64 Grange Rd | Eastern Heights | engagement | hold | — |
```

**Why per-client?** Clients may want to customize which properties to report on at any time.

---

### 3. Universal Report Generator Workflow (Make)

**Purpose:** Single workflow that generates reports for ANY client

**Inputs:**
- `client_id` (required) — which client to report on
- `ad_group_id` (optional) — specific property, or run all active

**Process:**

```
1. INPUT: client_id
   ↓
2. LOOKUP: Query Master Clients Sheet for client config
   ├─ Get Facebook Business ID
   ├─ Get Facebook Campaign ID
   ├─ Get Google Sheet ID
   ├─ Get Contact Email
   └─ Get GitHub Folder
   ↓
3. FETCH FACEBOOK DATA
   ├─ Pull active ad groups from campaign
   └─ For each ad group with status="green":
   ↓
4. PARSE AD GROUP NAME
   ├─ Extract property address
   ├─ Extract suburb
   ├─ Extract goal (traffic/lead-gen/awareness/sales/engagement)
   └─ Generate report slug (e.g., "1-38-cooinda-street")
   ↓
5. FETCH METRICS
   ├─ Based on goal, pull relevant metrics from Facebook Insights
   ├─ Example (traffic): reach, impressions, clicks, LPV, CTR, CPC, frequency
   └─ Example (engagement): messages, comments, shares, profile clicks
   ↓
6. GENERATE INSIGHTS (OpenAI)
   ├─ Select AI prompt based on goal
   ├─ Feed metrics + benchmarks + goal context
   └─ Generate 4 insight sections tailored to goal
   ↓
7. BUILD REPORT HTML
   ├─ Select report template based on goal
   ├─ Inject metrics + insights + property info
   └─ Generate interactive single-page report
   ↓
8. COMMIT TO GITHUB
   ├─ Path: /[client_id]/[property_slug]/index.html
   ├─ Example: /realestate4305/1-38-cooinda-street/index.html
   └─ Push to reports.noinkmarketing.com.au
   ↓
9. UPDATE GOOGLE SHEET
   ├─ Set "Last Report Sent" = today
   └─ Mark as complete
   ↓
10. SEND EMAIL
    ├─ To: Client email (from Master Clients Sheet)
    ├─ Subject: "[Property] — Your Campaign Report is Ready"
    └─ Include link to report
    ↓
11. DONE ✓
```

**Key Feature:** Workflow is identical for every client. Only the configuration (Master Sheet row) changes.

---

### 4. Client Provisioning Workflow (Make) — Optional Automation

**Purpose:** One-click setup for new clients

**Inputs:**
```
- Client Name
- Facebook Business ID
- Facebook Campaign ID
- Contact Email
- Google Sheet ID (or create template)
```

**Outputs:**
```
✓ New row added to Master Clients Sheet
✓ GitHub folder created
✓ Test report generated
✓ Confirmation email sent to Lara
```

**Result:** New clients can be added in 10 minutes instead of hours

---

### 5. GitHub Repository Structure

```
reports.noinkmarketing.com.au/
├── index.html (landing page)
├── CNAME (domain config)
├── realestate4305/ (Client 4305)
│   ├── 1-38-cooinda-street/
│   │   └── index.html
│   ├── 64-grange-rd/
│   │   └── index.html
│   └── ...
├── bronwyn/ (Client Bronwyn)
│   ├── property-1-name/
│   │   └── index.html
│   ├── property-2-name/
│   │   └── index.html
│   └── ...
├── client-three/ (Client 3)
│   └── ...
└── ...
```

**Access:** Each property report is publicly accessible at:
```
https://reports.noinkmarketing.com.au/[client_id]/[property_slug]/
```

---

## Data Flow Diagram

```
┌─────────────────────────────────────────────────────────┐
│         MASTER CLIENTS SHEET (Google Sheets)            │
│  Client IDs, FB Accounts, Campaign IDs, Emails, Sheets  │
└──────────────────────┬──────────────────────────────────┘
                       │
                       ↓
        ┌──────────────────────────────┐
        │  UNIVERSAL REPORT GENERATOR  │
        │  (Make Workflow — 1 per sys) │
        └──────────────────────────────┘
                       │
        ┌──────────────┴──────────────┐
        ↓                             ↓
   ┌─────────────────┐         ┌──────────────┐
   │ FACEBOOK INSIGHTS│         │ PER-CLIENT   │
   │ (Ad Group Data) │         │ GOOGLE SHEET │
   └────────┬────────┘         └──────────────┘
            │
        ┌───┴─────────────────┐
        ↓                     ↓
   ┌─────────────┐      ┌──────────────┐
   │ OpenAI GPT  │      │ Parse Ad Grp │
   │ (Insights)  │      │ Name→Goal    │
   └──────┬──────┘      └──────────────┘
          │
          └──────────────┬──────────────┐
                         ↓              ↓
                    ┌─────────┐   ┌──────────┐
                    │ GITHUB  │   │  EMAIL   │
                    │ COMMIT  │   │  SEND    │
                    └─────────┘   └──────────┘
```

---

## Workflow Triggers

### Option 1: Weekly Schedule (Recurring)
- Every Sunday 23:15 UTC
- Runs for all clients with status="active"
- Generates reports for all ad groups with status="green"

### Option 2: On-Demand (Manual)
- Lara selects client → workflow runs
- Useful for testing or ad-hoc reports

### Option 3: Webhook (Client-Initiated)
- Clients can trigger their own reports
- Requires webhook URL passed to client
- More advanced, optional feature

---

## Key Advantages

✅ **Scalable** — Add 100 clients without touching Make workflows  
✅ **Data-Driven** — No code changes, just Google Sheet rows  
✅ **Goal-Aware** — Reports adapt based on campaign goal  
✅ **Automated** — Minimal manual intervention  
✅ **Isolated** — Kent's system completely separate  
✅ **Maintainable** — One workflow to debug/improve, not dozens  
✅ **Client-Ready** — Reports auto-customized per goal  

---

## Constraints & Rules

⚠️ **Ad Group Naming:** Must follow `[property]--[suburb]--[goal]` format  
⚠️ **One Campaign Per Client:** Simplifies configuration  
⚠️ **One Goal Per Ad Group:** Each property has one goal (can run multiple ads)  
⚠️ **Google Sheets Required:** For tracking active ad groups per client  
⚠️ **Kent Isolated:** His system never touched or referenced  

---

**Last Updated:** 2026-09-08  
**Status:** ARCHITECTURE REFERENCE — Review before building workflows
