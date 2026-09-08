# Client Provisioning Workflow (Make Blueprint)

## Purpose

One Make scenario that takes a handful of inputs for a brand-new client and does
all the repetitive setup automatically — so onboarding a new client is filling in
a form, not building infrastructure.

**This workflow does NOT touch Kent's system in any way.** It only ever writes to
the Master Clients Sheet, creates a new per-client Google Sheet from a template,
and creates a new folder in this repo.

---

## Scenario Name

`Client Provisioning — Standard Clients`

Same Make folder as the Universal Report Generator: `Standard Client System`.

---

## Trigger

**On-demand**, run manually by Lara with a small set of inputs (scenario input
variables, or a simple Google Form that writes to a "New Client Requests" sheet
which this scenario watches).

## Inputs Required

| Input | Example | Where It Comes From |
|---|---|---|
| Client Name | `Bronwyn Scott` | Lara types it |
| Client ID | `bronwyn` | Lara chooses (lowercase, no spaces) |
| Facebook Business ID | `654321098765432` | Facebook Business Manager |
| Facebook Campaign ID | `987654321` | Facebook Ads Manager |
| Contact Email | `bronwyn@email.com` | Client's email |
| GitHub Folder | `bronwyn` | Usually same as Client ID |

---

## Module-by-Module Blueprint

### 1. Trigger — Manual Run / Watch New Client Requests
- If using a Google Form → New Row trigger on "New Client Requests" sheet
- If manual → scenario run with input variables set each time

### 2. Validate Inputs
- Router: check Client ID doesn't already exist in Master Clients Sheet (Search Rows, filter by Client ID)
- If duplicate found → stop, notify Lara, do not proceed
- If Facebook Business ID / Campaign ID are empty → stop, notify Lara

### 3. Copy Client Tracker Sheet Template (Google Sheets — or Google Drive "Copy File")
- Source: a saved blank template following `06_CLIENT_TRACKER_SHEET_TEMPLATE.md` structure
- Destination name: `{{Client Name}} — Campaign Tracker`
- Output: new Sheet ID

### 4. Add Row to Master Clients Sheet (Google Sheets — Add Row)
- Client ID, Client Name, FB Business ID, FB Campaign ID, Contact Email,
  Google Sheet ID (from Step 3), GitHub Folder, Status = `active`,
  Created Date = today, Notes = blank

### 5. Create GitHub Folder Placeholder (GitHub — Create File)
- Path: `/{{GitHub Folder}}/.gitkeep` (or a small `README.md` stating the client name)
- This ensures the folder exists in the repo before the first real report lands there

### 6. Test Run — Trigger Universal Report Generator (Make — Run a Scenario, or manual follow-up)
- Optional but recommended: fire the Universal Report Generator for this one
  new `client_id` immediately, so Lara can see a real report before telling
  the client it's live
- **Note:** this step will produce nothing until the client has actually added
  at least one row with `Status = green` to their new Client Tracker Sheet —
  so in practice this is often a manual step after Lara adds the first property

### 7. Notify Lara (Email or Slack/Telegram — reuse whichever channel she already uses)
- "✅ New client provisioned: {{Client Name}}"
- Includes: Client ID, link to their new Google Sheet, GitHub folder path
- Includes a reminder: "Add at least one active ad group to their tracker
  sheet before the next scheduled report run"

---

## What Lara Still Does Manually (By Design)

Provisioning automates the *infrastructure*. It does not automate:
- Setting up the client's Facebook campaign/ad groups (that's ad ops, not a data step)
- Adding the first ad group rows to their new Client Tracker Sheet (client-specific, changes over time)
- Naming ad groups correctly (client's team follows `02_AD_GROUP_NAMING_CONVENTION.md`)

This keeps the automation safe — it never guesses at campaign structure, it only
sets up the plumbing.

---

## Result

**New client setup time:** ~10-15 minutes (mostly Lara typing 6 values and adding
the first property rows), instead of hours duplicating Make scenarios by hand.

---

**Last Updated:** 2026-09-08
**Status:** BLUEPRINT — build as a new scenario, separate from Kent's workspace entirely
