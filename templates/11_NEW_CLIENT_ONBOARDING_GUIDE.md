# New Client Onboarding Guide

## Quick Reference

This is the practical checklist for adding a new client to the Standard Client
Reporting System. Follow this exactly — it assumes the Universal Report Generator
and Client Provisioning workflows (docs 07 & 10) are already built in Make.

**⏱️ Total time: ~15-20 minutes** (vs. 3+ hours duplicating workflows manually)

**🔒 Reminder:** This system is completely separate from Kent. Never reference or
touch Kent's workflows/sheets while onboarding a new client here.

---

## Before You Start — Confirm With The Client

- [ ] Client has a Facebook Business Manager account with ads access
- [ ] Client has (or will set up) **one campaign** containing all property ad groups
- [ ] Client understands the ad group naming rule (send them `02_AD_GROUP_NAMING_CONVENTION.md`
      or just the short version below)
- [ ] Client has confirmed a contact email for report delivery

### Ad Group Naming — What To Tell The Client

> "Please name each ad group exactly like this:
> `property-address--suburb--goal`
> Example: `1-38-cooinda-street--eastern-heights--traffic`
> Use double hyphens (`--`) between each part. Goal must be one of:
> `traffic`, `lead-generation`, `awareness`, `sales`, `engagement`."

---

## Step-by-Step

### 1. Gather the 6 inputs
- [ ] Client Name
- [ ] Client ID (lowercase, no spaces — e.g. `bronwyn`, `client-abc`)
- [ ] Facebook Business ID (from Business Manager → Settings)
- [ ] Facebook Campaign ID (from Ads Manager → the one campaign for this client)
- [ ] Contact Email
- [ ] GitHub Folder (usually same as Client ID)

### 2. Run the Client Provisioning Workflow
- [ ] Open Make → `Standard Client System` folder → `Client Provisioning — Standard Clients`
- [ ] Run with the 6 inputs from Step 1
- [ ] Confirm the notification: new Master Clients Sheet row created, new
      Client Tracker Sheet created, GitHub folder created

### 3. Populate the Client's Tracker Sheet
- [ ] Open the new Client Tracker Sheet (link from provisioning notification)
- [ ] For each property/ad group the client already has (or as they're created):
  - Add Ad Group ID (from Facebook Ads Manager)
  - Add Ad Group Name (must match naming convention exactly)
  - Set Status = `green` for anything ready to report on
- [ ] Columns C/D/E (Property, Suburb, Goal) auto-fill if you've set up the
      regex formulas from `06_CLIENT_TRACKER_SHEET_TEMPLATE.md`

### 4. Test Run
- [ ] In Make, run the Universal Report Generator scenario on-demand,
      filtered to this one `client_id`
- [ ] Check GitHub: does `/{{GitHub Folder}}/{{property-slug}}/index.html` exist and look right?
- [ ] Check email: did the test report arrive correctly formatted for the goal?
- [ ] Spot-check the report against the goal — does a `traffic` ad group show
      a traffic-flavoured report, not a generic one?

### 5. Go Live
- [ ] Confirm Master Clients Sheet row has `Status = active`
- [ ] Confirm the client is included in the next scheduled run (Sunday 23:15)
- [ ] Let the client know their reporting is live and where to find reports:
      `https://reports.noinkmarketing.com.au/{{GitHub Folder}}/`

---

## Ongoing Maintenance (Not Just Onboarding)

- **New property added by client:** just add a new row to their Client Tracker
  Sheet with `Status = green` — no Make changes needed
- **Property paused:** set `Status = hold` on that row
- **Property sold/removed:** set `Status = inactive`
- **Client pauses entirely:** set `Status = inactive` on their row in the
  Master Clients Sheet (keeps history, excludes from scheduled runs)

---

## If Something Goes Wrong

| Symptom | Likely Cause | Fix |
|---|---|---|
| No report generated for a property | Ad group name doesn't match convention | Check `02_AD_GROUP_NAMING_CONVENTION.md`, fix the name in Facebook and the sheet |
| Report generated but shows wrong goal's content | Goal typo in ad group name (e.g. `traffik`) | Must be exactly one of the 5 valid goals |
| Workflow can't find client | Master Clients Sheet row missing or `Status ≠ active` | Check row 05_MASTER_CLIENTS_SHEET_TEMPLATE.md structure |
| Email didn't send | Contact Email wrong/blank in Master Clients Sheet | Fix the email, re-run manually |
| Report page 404s | GitHub Folder mismatch between Master Sheet and actual repo folder | Confirm exact spelling matches |

---

**Last Updated:** 2026-09-08
**Status:** OPERATIONAL GUIDE — use this every time you onboard a new client
