# Ad Group Naming Convention

## Format (REQUIRED)

```
[property-address-hyphenated]--[suburb]--[goal]
```

**Delimiter:** Double hyphen (`--`)  
**Property address:** Hyphenated (e.g., `1-38-cooinda-street`)  
**Suburb:** Hyphenated if multi-word (e.g., `eastern-heights`)  
**Goal:** One of the 5 allowed values (see below)  

## Examples

| Property | Suburb | Goal | Ad Group Name |
|----------|--------|------|---------------|
| 1/38 Cooinda Street | Eastern Heights | Traffic | `1-38-cooinda-street--eastern-heights--traffic` |
| 47 White Gums Road | Hattonvale | Lead Generation | `47-white-gums-rd--hattonvale--lead-generation` |
| 64 Grange Road | Eastern Heights | Engagement | `64-grange-rd--eastern-heights--engagement` |
| Some Property Lane | Somewhere Suburb | Awareness | `some-property-lane--somewhere-suburb--awareness` |

## Valid Goals

### 1. **traffic**
- **Focus:** Driving people to the listing page
- **Key Metrics:** Landing Page Views, CTR, Cost per Click, Reach, Frequency
- **Report Emphasis:** "X people visited the listing"

### 2. **lead-generation**
- **Focus:** Capturing qualified leads (form fills, inquiries)
- **Key Metrics:** Form Submissions, Cost per Lead, Conversion Rate, Lead Quality
- **Report Emphasis:** "X qualified leads generated"

### 3. **awareness**
- **Focus:** Maximum visibility and reach
- **Key Metrics:** Reach, Impressions, Frequency, CPM, Cost per Reach
- **Report Emphasis:** "X people learned about this property"

### 4. **sales**
- **Focus:** Moving toward conversion/sales
- **Key Metrics:** Engagement, Video Plays, Add-to-Cart, Link Clicks, Saves
- **Report Emphasis:** "X people showed buying intent"

### 5. **engagement**
- **Focus:** Direct interaction and communication
- **Key Metrics:** Messages, Comments, Shares, Direct Messages, Profile Clicks, Saves
- **Report Emphasis:** "X people engaged directly (messages, comments, shares)"

## Regex Pattern (For Workflow Parsing)

```regex
^(.+?)--(.+?)--(.+)$
```

**Groups:**
- Group 1: Property address (everything before first `--`)
- Group 2: Suburb (everything between first `--` and second `--`)
- Group 3: Goal (everything after second `--`)

### JavaScript Example

```javascript
const adGroupName = "1-38-cooinda-street--eastern-heights--traffic";
const regex = /^(.+?)--(.+?)--(.+)$/;
const match = adGroupName.match(regex);

const property = match[1];  // "1-38-cooinda-street"
const suburb = match[2];    // "eastern-heights"
const goal = match[3];      // "traffic"
```

### Python Example

```python
import re

ad_group_name = "1-38-cooinda-street--eastern-heights--traffic"
pattern = r"^(.+?)--(.+?)--(.+)$"
match = re.match(pattern, ad_group_name)

property_addr = match.group(1)  # "1-38-cooinda-street"
suburb = match.group(2)          # "eastern-heights"
goal = match.group(3)            # "traffic"
```

## Why This Matters

1. **No Manual Config** — Goal is encoded in the name
2. **Automation Ready** — Workflow automatically selects report type
3. **Scalable** — Works for any number of ad groups
4. **Self-Documenting** — Anyone can see the property + goal at a glance
5. **No Room for Error** — Format is rigid, parsing is deterministic

## Enforcement

✅ **When creating ad groups in Facebook**, use this format exactly  
✅ **When onboarding clients**, confirm they follow this format  
✅ **In the workflow**, validate the format before processing  

---

**Last Updated:** 2026-09-08  
**Status:** LOCKED FORMAT — DO NOT DEVIATE
