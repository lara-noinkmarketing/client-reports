# Report HTML Templates — KPI Variants by Goal

## Purpose

The report HTML itself (dark hero header, red accent, card-based layout, funnel visual,
tabs) stays **one shared template** — same CSS, same structure as the current live
reports (see `realestate4305/*/index.html` for the reference build). Only the
**hero headline, 3 hero-stat chips, and 4 KPI cards** change per goal, driven by
Step 7's Router branch in the Universal Report Generator workflow.

This keeps visual consistency across every client/property while letting the report
"speak the language" of the campaign's actual goal.

---

## Shared Structure (Unchanged Across All Goals)

- Sticky top nav with No Ink Marketing logo → links to noinkmarketing.com.au
- Dark hero section: badge, headline, subhead, reporting period pill
- 3 hero-stat chips under headline
- 4-card KPI grid
- "Plain English snapshot" — 4 insight items from OpenAI (VISIBILITY / ENGAGEMENT / [goal label] / SOCIAL PROOF)
- Cost efficiency card
- Traffic funnel visual (steps + ratio bars)
- Footer with property + reporting period

---

## 1. Traffic

**Headline pattern:** `"Over {{lpv}} people visited the listing in {{days}} days!"`

**Hero-stat chips:**
1. Ad Spend — `${{spend}}`
2. Listing Page Views — `{{lpv}}`
3. Ad Click-Through Rate — `{{ctr}}%`

**KPI cards:**
1. Property exposure — `{{reach}}` — "People reached by the campaign"
2. Listing Page Views — `{{lpv}}` — "People who clicked through to view the listing"
3. Ad clicks — `{{clicks}}` — "Total clicks on the ad"
4. Cost per view — `${{cost_per_lpv}}` — "Average cost per listing visit"

---

## 2. Lead Generation

**Headline pattern:** `"{{leads}} qualified leads generated in {{days}} days!"`

**Hero-stat chips:**
1. Ad Spend — `${{spend}}`
2. Leads Captured — `{{leads}}`
3. Cost Per Lead — `${{cost_per_lead}}`

**KPI cards:**
1. Property exposure — `{{reach}}` — "People reached by the campaign"
2. Leads captured — `{{leads}}` — "Qualified inquiries submitted"
3. Conversion rate — `{{conversion_rate}}%` — "Clicks that became leads"
4. Cost per lead — `${{cost_per_lead}}` — "Average cost per qualified inquiry"

---

## 3. Awareness

**Headline pattern:** `"{{reach}} local buyers reached in {{days}} days!"`

**Hero-stat chips:**
1. Ad Spend — `${{spend}}`
2. People Reached — `{{reach}}`
3. Avg. Frequency — `{{frequency}}x`

**KPI cards:**
1. Property exposure — `{{reach}}` — "Unique people who saw the property"
2. Impressions — `{{impressions}}` — "Total times the ad appeared"
3. Cost per reach — `${{cost_per_reach}}` — "Cost to reach each person"
4. CPM — `${{cpm}}` — "Cost per 1,000 impressions"

---

## 4. Sales / Consideration

**Headline pattern:** `"{{engagements}} buyers showed active interest in {{days}} days!"`

**Hero-stat chips:**
1. Ad Spend — `${{spend}}`
2. Total Engagements — `{{engagements}}`
3. Engagement Rate — `{{engagement_rate}}%`

**KPI cards:**
1. Property exposure — `{{reach}}` — "People reached by the campaign"
2. Video plays — `{{video_plays}}` — "Video views across Facebook and Instagram"
3. Saves — `{{saves}}` — "People who saved the listing"
4. Engagement rate — `{{engagement_rate}}%` — "Share of reached audience who interacted"

---

## 5. Engagement

**Headline pattern:** `"{{messages}} buyers messaged directly about this property!"`

**Hero-stat chips:**
1. Ad Spend — `${{spend}}`
2. Direct Messages — `{{messages}}`
3. Cost Per Message — `${{cost_per_message}}`

**KPI cards:**
1. Property exposure — `{{reach}}` — "People reached by the campaign"
2. Direct messages — `{{messages}}` — "People who messaged about the property"
3. Comments + shares — `{{comments_plus_shares}}` — "Public interaction on the ad"
4. Message rate — `{{message_rate}}%` — "Share of reached audience who messaged directly"

---

## Implementation Note for Make

Rather than maintain 5 separate HTML blueprints, build **one HTML template** with
placeholder tokens for headline, 3 hero-stat labels/values, and 4 KPI card
labels/values. Step 7's Router sets a small set of "Set Variables" per branch
(`hero_headline`, `chip1_label`, `chip1_value`, ... `kpi4_label`, `kpi4_value`)
using the tables above, then Step 12 (Build Report HTML) fills the one shared
template. This avoids five near-duplicate HTML-builder modules to maintain.

The "Plain English snapshot" section's 4 items come directly from the OpenAI output
(doc 08) — no separate mapping needed there.

---

**Last Updated:** 2026-09-08
**Status:** REFERENCE — pairs with `07_UNIVERSAL_REPORT_GENERATOR_WORKFLOW.md` Step 12
