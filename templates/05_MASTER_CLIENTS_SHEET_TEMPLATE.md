# Master Clients Sheet Template

## Overview

The **Master Clients Sheet** is a single Google Sheet that stores configuration for ALL clients in one place.

This is the **source of truth** for the Universal Report Generator workflow.

---

## Sheet Name

Name the sheet: `Master Clients` (in your main Make or operations Google Sheet)

---

## Columns (Exact Order Required)

| # | Column Name | Type | Required | Example | Notes |
|---|------------|------|----------|---------|-------|
| A | Client ID | Text | ✓ | `4305` | Unique identifier. Used in GitHub folder names. |
| B | Client Name | Text | ✓ | `Real Estate 4305` | Friendly name for reference. |
| C | Facebook Business ID | Number | ✓ | `725040197894743` | From Facebook Business Manager. |
| D | Facebook Campaign ID | Number | ✓ | `123456789` | The single campaign that contains all properties. |
| E | Contact Email | Email | ✓ | `lara@client.com.au` | Where reports are sent. |
| F | Google Sheet ID | Text | ✓ | `1xI6Ex-EL...` | Sheet that tracks their ad groups. |
| G | GitHub Folder | Text | ✓ | `realestate4305` | Folder in reports.noinkmarketing.com.au |
| H | Status | Dropdown | ✓ | `active` | `active` or `inactive` (only active clients get reports) |
| I | Created Date | Date | ✓ | `2026-03-15` | When client was added to system. |
| J | Notes | Text | — | `Test client` | Optional internal notes. |

---

## Column Descriptions

### A: Client ID
- **Purpose:** Unique identifier for the client
- **Format:** Alphanumeric (no spaces)
- **Examples:** `4305`, `bronwyn`, `kent` (Kent stored separately, never in this list)
- **Used In:** GitHub folder names, workflow references
- **Validation:** Must be unique, no duplicates

### B: Client Name
- **Purpose:** Human-readable name
- **Format:** Any text
- **Examples:** `Real Estate 4305`, `Bronwyn Scott`, `New Client ABC`
- **Used In:** Email subjects, reports, notifications

### C: Facebook Business ID
- **Purpose:** Identifies which Facebook Business Manager account to pull data from
- **Format:** Numeric ID from Facebook Business Manager
- **Where to Find:** Facebook Business Manager → Settings → Business ID
- **Example:** `725040197894743`
- **Validation:** Must be valid Facebook Business ID

### D: Facebook Campaign ID
- **Purpose:** The specific campaign that contains all this client's ad groups
- **Format:** Numeric ID from Facebook Ads Manager
- **Important:** Only ONE campaign per client
- **Where to Find:** Facebook Ads Manager → Campaigns → Campaign ID
- **Example:** `123456789`
- **Validation:** Must be valid campaign within the Business ID

### E: Contact Email
- **Purpose:** Where to send completed reports
- **Format:** Valid email address
- **Examples:** `lara@noinkmarketing.com.au`, `client@domain.com`
- **Used In:** Email recipient when reports are generated
- **Validation:** Must be valid email format

### F: Google Sheet ID
- **Purpose:** Points to the per-client tracker sheet
- **Format:** Long alphanumeric string from sheet URL
- **Where to Find:** Open Google Sheet → URL: `docs.google.com/spreadsheets/d/[THIS_ID]/...`
- **Example:** `1xI6Ex-ELuoalFWEoZ-0PIewEJAK5PCzyt5zG3Pex8O0`
- **Used In:** Workflow reads this sheet to find active ad groups
- **Note:** This sheet must follow the Client Tracker Sheet template

### G: GitHub Folder
- **Purpose:** Folder in the GitHub repo where reports are stored
- **Format:** Lowercase, hyphen-separated
- **Examples:** `realestate4305`, `bronwyn`, `client-three`
- **Used In:** Report URLs: `reports.noinkmarketing.com.au/[folder]/[property]/`
- **Validation:** Must match GitHub folder that exists (or will be created)

### H: Status
- **Purpose:** Control whether client is active in the system
- **Format:** Dropdown: `active` | `inactive`
- **Default:** `active`
- **Used In:** Workflow filters — only processes active clients
- **When to Change:**
  - Set to `inactive` if client pauses (don't delete, keeps history)
  - Set to `active` when they resume

### I: Created Date
- **Purpose:** Track when client was added to system
- **Format:** Date (YYYY-MM-DD)
- **Example:** `2026-03-15`
- **Validation:** Must be valid date
- **Note:** This is informational; doesn't affect workflow

### J: Notes
- **Purpose:** Internal reference information
- **Format:** Any text
- **Examples:** `Test client`, `Integrated with CRM`, `High volume`, `V2 system`
- **Validation:** Optional field

---

## Example Data

```
Client ID | Client Name | FB Business ID | FB Campaign ID | Contact Email | Google Sheet ID | GitHub Folder | Status | Created | Notes
----------|-------------|----------------|----------------|---------------|-----------------|---------------|--------|---------|-------
4305 | Real Estate 4305 | 725040197894743 | 123456789 | lara@noinkmarketing.com.au | 1xI6Ex-EL... | realestate4305 | active | 2026-03-15 | Original client
5000 | Bronwyn Scott | 654321098765432 | 987654321 | bronwyn@email.com | 1abc2def3... | bronwyn | active | 2026-08-05 | V2 system
5001 | Client Three ABC | 111222333444555 | 555666777 | client3@domain.com | 5ghi6jkl7... | client-three | active | 2026-09-01 | New Q4 client
5002 | Future Client | 999888777666555 | 444333222 | future@email.com | pending | client-four | inactive | 2026-09-08 | Setup in progress
```

---

## How the Workflow Uses This

**Step 1: Read Master Clients Sheet**
```
FOR each row where Status = "active":
  Get Client ID, FB Account ID, Campaign ID, Email, Sheet ID, GitHub Folder
```

**Step 2: Fetch Ad Groups**
```
QUERY Facebook API:
  Account: [FB Business ID from column C]
  Campaign: [FB Campaign ID from column D]
  → Returns list of all ad groups in that campaign
```

**Step 3: Filter Active Ad Groups**
```
QUERY per-client Google Sheet [column F]:
  Get all rows where Status = "green"
  → This is the list of properties to report on
```

**Step 4: For Each Active Ad Group**
```
PARSE ad group name: [property]--[suburb]--[goal]
PULL Facebook Insights
GENERATE Report
COMMIT to GitHub at: /[GitHub Folder]/[property_slug]/index.html
SEND Email to: [Contact Email]
UPDATE Google Sheet: set Last Report Sent = today
```

---

## Setup Steps

1. **Create a Google Sheet** (if you don't have one already)
   - Use Google Sheets shared across your Make/operations workspace

2. **Add a new tab** called `Master Clients`

3. **Add headers** (Row 1):
   ```
   Client ID | Client Name | Facebook Business ID | Facebook Campaign ID | Contact Email | Google Sheet ID | GitHub Folder | Status | Created Date | Notes
   ```

4. **Add your first client row** (Row 2):
   ```
   4305 | Real Estate 4305 | 725040197894743 | 123456789 | lara@noinkmarketing.com.au | 1xI6Ex-EL... | realestate4305 | active | 2026-03-15 | 
   ```

5. **Save the sheet**

6. **Copy the Sheet ID** from the URL (everything after `/d/` and before `/edit`)

7. **Give this Sheet ID to Make workflow** when setting up Universal Report Generator

---

## Data Validation Rules

| Column | Rule |
|--------|------|
| Client ID | Must be unique, no spaces, alphanumeric only |
| Client Name | Must not be empty |
| FB Business ID | Must be numeric, valid Facebook ID |
| FB Campaign ID | Must be numeric, valid campaign ID |
| Contact Email | Must be valid email format |
| Google Sheet ID | Must be valid Google Sheet ID (test by pasting in URL) |
| GitHub Folder | Must be lowercase, hyphens OK, no spaces |
| Status | Must be `active` or `inactive` (case-sensitive) |
| Created Date | Must be valid date |

---

## Tips

✅ **Keep it organized:** Sort by Client ID or Created Date  
✅ **Archive old clients:** Set Status = `inactive`, don't delete  
✅ **Test URLs:** Paste Google Sheet IDs into a URL to verify they're valid  
✅ **Backup regularly:** Export as CSV weekly  
✅ **Document changes:** Use Notes column to track setup/issues  

---

## Troubleshooting

**Workflow can't find client:**
- Check: Is Status = `active`?
- Check: Is Client ID spelled correctly?
- Check: Are all required columns filled?

**Workflow fails to pull Facebook data:**
- Check: Is Facebook Business ID valid?
- Check: Is Facebook Campaign ID valid?
- Check: Do the Make credentials have access to this Business ID?

**Workflow can't read Google Sheet:**
- Check: Is Google Sheet ID correct?
- Check: Does Make have permission to access the sheet?
- Check: Does the sheet follow Client Tracker template?

---

**Last Updated:** 2026-09-08  
**Status:** TEMPLATE — Copy this structure for your Master Clients Sheet
