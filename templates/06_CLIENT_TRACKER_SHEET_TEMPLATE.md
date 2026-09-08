# Client Tracker Sheet Template

## Overview

Each client needs their own **Google Sheet** that tracks which ad groups/properties to report on.

This sheet is **per-client** and referenced in the Master Clients Sheet (column F: Google Sheet ID).

---

## Sheet Name

Name the sheet: `[Client Name] — Campaign Tracker`

Examples:
- `Real Estate 4305 — Campaign Tracker`
- `Bronwyn Scott — Campaign Tracker`
- `Client Three — Campaign Tracker`

---

## Columns (Exact Order Required)

| # | Column Name | Type | Required | Example | Notes |
|---|------------|------|----------|---------|-------|
| A | Ad Group ID | Number | ✓ | `52593735898220` | From Facebook Ads Manager. |
| B | Ad Group Name | Text | ✓ | `1-38-cooinda-st--eastern-heights--traffic` | Must follow naming convention. |
| C | Property Address | Text | ✓ | `1/38 Cooinda Street` | Extracted from ad group name (for reference). |
| D | Suburb | Text | ✓ | `Eastern Heights` | Extracted from ad group name (for reference). |
| E | Goal | Dropdown | ✓ | `traffic` | Extracted from ad group name. Options: traffic, lead-generation, awareness, sales, engagement |
| F | Status | Dropdown | ✓ | `green` | `green` = report, `hold` = skip, `inactive` = ignore |
| G | Last Report Sent | Date | — | `2026-09-08` | Auto-updated by workflow. |
| H | Notes | Text | — | `High performer` | Optional client notes. |

---

## Column Descriptions

### A: Ad Group ID
- **Purpose:** Unique Facebook identifier for this ad group
- **Format:** Numeric ID from Facebook Ads Manager
- **Where to Find:** Facebook Ads Manager → Campaigns → Ad Set ID
- **Example:** `52593735898220`
- **Validation:** Must be numeric, no spaces
- **Who fills this:** Lara or client (usually copied from Facebook)

### B: Ad Group Name
- **Purpose:** The Facebook ad group name — must follow naming convention
- **Format:** `[property-address-hyphenated]--[suburb]--[goal]`
- **Example:** `1-38-cooinda-street--eastern-heights--traffic`
- **Validation:** Must match regex: `^(.+?)--(.+?)--(.+)$`
- **Important:** If name doesn't match convention, workflow will skip it
- **Reference:** See `02_AD_GROUP_NAMING_CONVENTION.md`

### C: Property Address
- **Purpose:** Human-readable address (extracted from ad group name for reference)
- **Format:** Standard address format
- **Example:** `1/38 Cooinda Street`
- **Auto-populated:** Yes — Use formula to extract from column B
- **Formula:** Use regex or text functions to extract first section before `--`
- **Note:** This is read-only, derived from B

### D: Suburb
- **Purpose:** Suburb/area (extracted from ad group name for reference)
- **Format:** Standard suburb name
- **Example:** `Eastern Heights`
- **Auto-populated:** Yes — Extract from column B
- **Formula:** Extract middle section between first `--` and second `--`
- **Note:** This is read-only, derived from B

### E: Goal
- **Purpose:** Campaign goal (extracted from ad group name, determines report type)
- **Format:** Dropdown with 5 options
  - `traffic` — Drive listing page views
  - `lead-generation` — Capture inquiries/leads
  - `awareness` — Maximum visibility
  - `sales` — Move toward conversion
  - `engagement` — Direct messages/interaction
- **Example:** `traffic`
- **Auto-populated:** Yes — Extract from column B
- **Formula:** Extract last section after second `--`
- **Validation:** Must be one of the 5 valid goals
- **Reference:** See `03_METRICS_MAPPING_BY_GOAL.md`

### F: Status
- **Purpose:** Control whether this property gets reported on
- **Format:** Dropdown with 3 options
  - `green` — Report on this property (active)
  - `hold` — Skip reporting (temporarily paused)
  - `inactive` — Ignore completely (archived)
- **Default:** `green`
- **Used By:** Workflow filters for `Status = "green"` only
- **When to Use:**
  - `green`: Property is actively being advertised, report it
  - `hold`: Ad is paused or under review, don't report yet
  - `inactive`: Property sold or removed, skip permanently

### G: Last Report Sent
- **Purpose:** Track when the last report was generated
- **Format:** Date (YYYY-MM-DD)
- **Example:** `2026-09-08`
- **Updated By:** Workflow automatically sets this after generating report
- **Manual Setting:** Don't edit (workflow manages this)
- **Used For:** Reference — see when last report was sent

### H: Notes
- **Purpose:** Internal notes about this property/ad group
- **Format:** Any text
- **Examples:** `High performer`, `Pending review`, `High volume property`, `Video performing well`
- **Validation:** Optional field
- **Used For:** Internal reference only

---

## Example Data

```
Ad Group ID | Ad Group Name | Property Address | Suburb | Goal | Status | Last Report Sent | Notes
------------|---------------|------------------|--------|------|--------|------------------|-------
52593735898220 | 1-38-cooinda-street--eastern-heights--traffic | 1/38 Cooinda Street | Eastern Heights | traffic | green | 2026-09-08 | Good performer
52593735898221 | 47-white-gums-rd--hattonvale--lead-generation | 47 White Gums Road | Hattonvale | lead-generation | green | 2026-09-08 | Capturing leads
52593735898222 | 64-grange-rd--eastern-heights--engagement | 64 Grange Road | Eastern Heights | engagement | green | 2026-09-07 | Lots of messages
52593735898223 | some-property--suburb--awareness | Some Property | Suburb | awareness | hold | — | Paused, rework creative
52593735898224 | sold-property--area--traffic | Sold Property | Area | traffic | inactive | 2026-08-15 | Property sold - archived
```

---

## Setup Steps

1. **Create a Google Sheet** (or copy template sheet)
   - Title: `[Client Name] — Campaign Tracker`

2. **Add headers** (Row 1):
   ```
   Ad Group ID | Ad Group Name | Property Address | Suburb | Goal | Status | Last Report Sent | Notes
   ```

3. **Set up dropdowns** (Column E & F):
   - **Column E (Goal):** Data Validation → List → `traffic, lead-generation, awareness, sales, engagement`
   - **Column F (Status):** Data Validation → List → `green, hold, inactive`

4. **Add your first ad group row** (Row 2):
   ```
   52593735898220 | 1-38-cooinda-street--eastern-heights--traffic | 1/38 Cooinda Street | Eastern Heights | traffic | green | 2026-09-08 | 
   ```

5. **Share this sheet** with Make (if using service account):
   - Get Make's email
   - Add as Editor to sheet
   - Copy sheet ID to Master Clients Sheet

6. **Test the connection:**
   - Verify Make can read this sheet
   - Run a test report to confirm workflow finds ad groups

---

## Auto-Population Formulas

If you want to auto-populate columns C, D, E from the ad group name:

### Column C: Property Address
```
=REGEXEXTRACT(B2,"^(.+?)--")
```

### Column D: Suburb
```
=REGEXEXTRACT(B2,"--(.+?)--")
```

### Column E: Goal
```
=REGEXEXTRACT(B2,"--(.+)$")
```

**Note:** Only works if ad group name exactly matches convention. If it doesn't match, formula returns error (which is correct — alerts you to fix the name).

---

## Workflow Integration

**When workflow runs:**

1. **Reads Master Clients Sheet** to find this sheet's ID
2. **Queries all rows** where Status = `green`
3. **For each row:**
   - Pulls ad group ID from column A
   - Validates ad group name format (column B)
   - Extracts goal from ad group name (column E)
   - Fetches Facebook data for that ad group
   - Generates appropriate report based on goal
   - Commits report to GitHub
4. **Updates column G** with "Last Report Sent" = today's date
5. **Sends email** with report link

---

## Tips & Best Practices

✅ **Keep organized:** Sort by Suburb or Status  
✅ **Update status regularly:** Set to `hold` while ads are paused  
✅ **Archive old properties:** Set to `inactive` when property sells  
✅ **Use notes:** Document issues or high performers  
✅ **Test names:** Verify ad group names follow convention before adding  

---

## Common Issues

**Workflow says "Ad group name doesn't match convention"**
- Fix: Verify column B exactly matches: `[property]--[suburb]--[goal]`
- Example wrong: `1-38-cooinda - eastern-heights - traffic` (spaces instead of hyphens)
- Example right: `1-38-cooinda-street--eastern-heights--traffic` (double hyphens as delimiters)

**Workflow can't find this sheet**
- Check: Is the Google Sheet ID correct in Master Clients Sheet?
- Check: Does Make have Editor access to this sheet?
- Check: Are columns in correct order (A through H)?

**Report generated but Last Report Sent didn't update**
- May indicate partial success; check email was sent
- Workflow may have permissions issue updating sheet
- Verify Make has write access

**Seeing old/stale ad groups reporting**
- Fix: Set their Status to `inactive` to permanently exclude
- Use `hold` if you want to resume later

---

**Last Updated:** 2026-09-08  
**Status:** TEMPLATE — Create one per client
