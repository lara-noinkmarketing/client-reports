# Universal Report Generator Workflow (Make Blueprint)

## Purpose

This is the **one workflow** that generates reports for every standard client (not Kent). It's built by adapting the best parts of the Bronwyn workflows and the Real Estate 4305 one-off workflow.

**Base references (study these, do not edit them):**
- `Bronwyn - 0 - Campaign Detection` (id 5859876)
- `Bronwyn - 2 - Report Generator` (id 5977642)
- `Real Estate 4305 - One-Off Traffic Report (2 Ads)` (id 6193801)

This new workflow is a **separate, new scenario** — a fresh build that borrows their module patterns, not a duplicate of any one of them.

---

## Scenario Name

`Universal Report Generator — Standard Clients`

Keep it in its own Make folder: `Standard Client System` (separate from Kent's folder and from Bronwyn/4305 dev folders).

---

## Trigger

**Manual / On-Demand** to start (safest while testing), with a **weekly scheduled** copy once proven:

- On-demand: run by Lara selecting `client_id` as a scenario input
- Scheduled: Sunday 23:15 (matches existing cadence), loops through **every** row in Master Clients Sheet where `Status = active`

---

## Module-by-Module Blueprint

### 1. Get Master Clients (Google Sheets — Search Rows)
- Sheet: Master Clients Sheet (see `05_MASTER_CLIENTS_SHEET_TEMPLATE.md`)
- Filter: `Status = active` (and `Client ID = {{client_id}}` if run on-demand for one client)
- Output per client: FB Business ID, FB Campaign ID, Contact Email, Google Sheet ID, GitHub Folder

### 2. Iterator — Loop Each Active Client
- Iterates over rows from Step 1
- Everything below runs once per client

### 3. Get Client Tracker Rows (Google Sheets — Search Rows)
- Sheet ID: `{{Google Sheet ID}}` from Step 1 (dynamic — this is the key to reusability)
- Filter: `Status = green`
- Output: list of ad groups to report on for this client

### 4. Iterator — Loop Each Active Ad Group
- Iterates over rows from Step 3
- Everything below runs once per property

### 5. Validate Ad Group Name (Regexp — Match)
- Pattern: `^(.+?)--(.+?)--(.+)$`
- Input: `Ad Group Name` from Step 4
- **If no match:** Router branch → log error, skip this row, continue loop (never crash the whole run over one bad name)

### 6. Parse Property / Suburb / Goal (Regexp — Parser, 3x or one parser with groups)
- Group 1 → `property_slug`
- Group 2 → `suburb`
- Group 3 → `goal`
- Set Variables module to hold these cleanly for later steps

### 7. Router — Branch by Goal
Five routes, one per goal (`traffic`, `lead-generation`, `awareness`, `sales`, `engagement`), each filtered by `goal = "..."`. Each route:
- Pulls the metric set defined for that goal in `03_METRICS_MAPPING_BY_GOAL.md`
- Uses a goal-specific OpenAI prompt (see `08_GOAL_SPECIFIC_AI_PROMPTS.md`)
- Feeds a shared HTML template with different "hero stat" / KPI selections

This keeps one workflow but goal-appropriate output — this is the piece that makes the system "intelligent" per Lara's requirement.

### 8. Get Ad Set Insights (Facebook Insights — Get Ad Account/Ad Set Insights)
- Ad Set ID: `Ad Group ID` from Step 4
- Fields: pull the full superset of fields used across all 5 goals (cheaper than 5 separate calls) — reach, impressions, frequency, clicks, unique clicks, CTR, CPC, CPM, spend, actions (landing_page_view, lead, video_play, post_engagement, onsite_conversion.messaging_conversation_started_7d, comment, post_reaction, post save)
- Date range: since `Ad Group` start date or last 7/14/28 days (match existing Kent/Bronwyn convention)

### 9. Set Variables — Calculate Derived Metrics
- CTR, CPC, cost-per-LPV, cost-per-lead, cost-per-message, frequency, etc.
- Only the ones relevant to the branch's goal get surfaced downstream — but calculate the full set here once, reuse everywhere

### 10. OpenAI — Create Completion (goal-specific prompt)
- Model: GPT-4o (matches existing quality bar)
- Prompt: injected from the goal-specific template (see doc 08)
- Output: 4 insight sections in the same delimited format Kent/Bronwyn's workflow already uses, so the existing regex parser modules can be reused as-is

### 11. Regexp — Parse Insight Sections (x4)
- Same pattern as existing workflows — extracts each of the 4 insight paragraphs

### 12. Build Report HTML (Set Variable / Text aggregator)
- Uses the existing dark-hero / red-accent HTML template (same visual system as current reports)
- Swaps in goal-specific KPI cards and hero stat based on Step 7's branch
- See `09_REPORT_HTML_TEMPLATES.md` for the 5 KPI-card variants

### 13. GitHub — Check File Exists (Make REST API Call)
- Path: `/{{GitHub Folder}}/{{property_slug}}/index.html`
- Repo: `lara-noinkmarketing/client-reports` (this repo — NOT a new repo per client)

### 14. Router — File Exists? (Create vs Update)
- If not exists → GitHub Create File
- If exists → GitHub Update File (needs SHA from Step 13)

### 15. Update Client Tracker Sheet (Google Sheets — Update Row)
- Sheet: `{{Google Sheet ID}}` from Step 1
- Row: matched by `Ad Group ID`
- Set: `Last Report Sent = today`

### 16. Send Email (Google Email — Send an Email)
- To: `Contact Email` from Step 1
- Subject: `📊 {{property_address}}, {{suburb}} — Your Campaign Report is Ready`
- Body: compact stat summary + button linking to `https://reports.noinkmarketing.com.au/{{GitHub Folder}}/{{property_slug}}/`

### 17. End of Ad Group Loop → back to Step 4
### 18. End of Client Loop → back to Step 2

---

## Error Handling

- **Bad ad group name (Step 5 fails match):** log to an "Errors" tab in Master Clients Sheet (client, ad group ID, reason), skip, continue — never halt the whole run
- **Facebook API error (e.g., no data for date range):** skip that ad group, log, continue
- **GitHub commit failure:** retry once (Make's built-in retry), then log and continue
- **Whole scenario should never stop mid-run because one client or one property has an issue** — this is the #1 lesson from consolidating multiple client workflows into one

---

## What Makes This Reusable

| Old approach (per-client duplication) | New approach (this workflow) |
|---|---|
| Facebook Account ID hardcoded in module | Read from Master Clients Sheet row |
| Google Sheet ID hardcoded in module | Read from Master Clients Sheet row |
| Report goal assumed (usually "traffic") | Parsed from ad group name, branches automatically |
| One workflow per client | One workflow, N clients, N properties |
| GitHub repo/path hardcoded per client | Path built dynamically from `GitHub Folder` + `property_slug` |
| Adding a client = duplicate 2-3 scenarios | Adding a client = one row in a sheet |

---

## Build Order (Recommended)

1. Build Steps 1–6 first, test that client + ad group + goal parsing works with dummy data
2. Build Step 7 router with just the `traffic` branch wired end-to-end (Steps 8–16)
3. Test traffic branch fully against Real Estate 4305 or Bronwyn's live data (read-only test, don't send real emails until confirmed)
4. Duplicate the branch internals for the remaining 4 goals, swapping only the OpenAI prompt and KPI card selection
5. Wire scheduled trigger once all 5 goals are validated

---

**Last Updated:** 2026-09-08
**Status:** BLUEPRINT — build as a new scenario in Make, do not touch Kent's, Bronwyn's or 4305's existing scenarios
