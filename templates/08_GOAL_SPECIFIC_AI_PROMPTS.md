# Goal-Specific AI Prompts

## Purpose

The OpenAI module (Step 10 in the Universal Report Generator) needs a different prompt per goal so the 4 generated insight paragraphs talk about the right things. All 5 prompts follow the same **output contract** so the existing regex-parsing modules (borrowed from Kent/Bronwyn workflows) keep working without changes.

---

## Shared Output Contract (All Goals)

Every prompt must instruct GPT-4o to return exactly 4 labelled sections, plain English, no jargon, Australian tone, each 1–2 sentences:

```
VISIBILITY: [insight 1]
ENGAGEMENT: [insight 2]
[GOAL-SPECIFIC LABEL]: [insight 3]
SOCIAL PROOF: [insight 4]
```

This matches the delimited format the existing Regexp parser modules already extract (`VISIBILITY:`, `ENGAGEMENT:`, etc.) — keeping this contract means Step 11 (Regexp — Parse Insight Sections) doesn't need 5 different parser configs.

**Model settings (match existing):** GPT-4o, temperature 0.7, max tokens 700.

---

## 1. Traffic Prompt

```
You are writing a plain-English campaign summary for an Australian real estate
client. The campaign goal is TRAFFIC (driving people to view the property listing).

Property: {{property_address}}, {{suburb}}
Reporting period: {{date_range}}

Metrics:
- Reach: {{reach}}
- Impressions: {{impressions}}
- Frequency: {{frequency}}
- Ad clicks: {{clicks}}
- Click-through rate: {{ctr}}%
- Cost per click: ${{cpc}}
- Landing page views: {{lpv}}
- Cost per landing page view: ${{cost_per_lpv}}
- Spend: ${{spend}}

Australian real estate benchmarks: average CTR 4.28%, average CPM $30.09.

Write exactly 4 sections, 1-2 sentences each, no jargon, confident and warm tone:

VISIBILITY: [how many people saw it, frequency, whether that's healthy]
ENGAGEMENT: [click behaviour, CTR vs benchmark, cost efficiency of clicks]
LISTING TRAFFIC: [landing page views, cost per view, what that means practically]
SOCIAL PROOF: [any standout number that shows strong interest]
```

---

## 2. Lead Generation Prompt

```
You are writing a plain-English campaign summary for an Australian real estate
client. The campaign goal is LEAD GENERATION (capturing qualified inquiries).

Property: {{property_address}}, {{suburb}}
Reporting period: {{date_range}}

Metrics:
- Reach: {{reach}}
- Ad clicks: {{clicks}}
- Leads/form submissions: {{leads}}
- Cost per lead: ${{cost_per_lead}}
- Lead conversion rate: {{conversion_rate}}%
- Click-through rate: {{ctr}}%
- Spend: ${{spend}}

Typical Australian real estate lead cost range: $5-$25 depending on market.

Write exactly 4 sections, 1-2 sentences each, no jargon, confident and warm tone:

VISIBILITY: [reach and how many people were exposed to the ad]
ENGAGEMENT: [click behaviour and interest shown before converting to a lead]
LEAD QUALITY: [number of leads, cost per lead vs typical range, conversion rate]
SOCIAL PROOF: [any standout number showing strong buyer intent]
```

---

## 3. Awareness Prompt

```
You are writing a plain-English campaign summary for an Australian real estate
client. The campaign goal is AWARENESS (maximum visibility for the property).

Property: {{property_address}}, {{suburb}}
Reporting period: {{date_range}}

Metrics:
- Reach: {{reach}}
- Impressions: {{impressions}}
- Frequency: {{frequency}}
- Cost per 1,000 impressions (CPM): ${{cpm}}
- Cost per person reached: ${{cost_per_reach}}
- Spend: ${{spend}}

Australian real estate benchmark: average CPM $30.09, healthy frequency 1.5-2.5x.

Write exactly 4 sections, 1-2 sentences each, no jargon, confident and warm tone:

VISIBILITY: [total unique people reached, how that compares to a typical suburb audience]
ENGAGEMENT: [frequency - was it memorable without being repetitive]
MARKET REACH: [CPM and cost per person vs benchmark, what that means for budget efficiency]
SOCIAL PROOF: [any standout number showing strong market penetration]
```

---

## 4. Sales Prompt

```
You are writing a plain-English campaign summary for an Australian real estate
client. The campaign goal is SALES/CONSIDERATION (showing buying intent).

Property: {{property_address}}, {{suburb}}
Reporting period: {{date_range}}

Metrics:
- Reach: {{reach}}
- Video plays: {{video_plays}}
- Total engagements: {{engagements}}
- Engagement rate: {{engagement_rate}}%
- Saves: {{saves}}
- Click-through rate: {{ctr}}%
- Spend: ${{spend}}

Typical Australian real estate benchmarks: video play rate 10-20% of reach,
engagement rate 2-5%.

Write exactly 4 sections, 1-2 sentences each, no jargon, confident and warm tone:

VISIBILITY: [reach and how many people were exposed]
ENGAGEMENT: [total engagements and rate vs benchmark - what this signals about interest]
BUYING SIGNALS: [video plays, saves, and what these specific actions suggest about intent]
SOCIAL PROOF: [any standout number showing strong consideration/interest]
```

---

## 5. Engagement Prompt

```
You are writing a plain-English campaign summary for an Australian real estate
client. The campaign goal is ENGAGEMENT (direct messages and interaction).

Property: {{property_address}}, {{suburb}}
Reporting period: {{date_range}}

Metrics:
- Reach: {{reach}}
- Messages started: {{messages}}
- Message rate: {{message_rate}}%
- Comments: {{comments}}
- Shares: {{shares}}
- Cost per message: ${{cost_per_message}}
- Spend: ${{spend}}

Typical Australian real estate benchmark: message rate 0.5-2% of reach.
Direct messages are the strongest buyer intent signal available.

Write exactly 4 sections, 1-2 sentences each, no jargon, confident and warm tone:

VISIBILITY: [reach and how many people were exposed]
ENGAGEMENT: [comments and shares - public interaction signals]
DIRECT INTEREST: [messages, message rate vs benchmark, cost per message - frame this
as the strongest buying signal since these are people actively reaching out]
SOCIAL PROOF: [any standout number showing strong direct interest]
```

---

## Notes on Variable Names

The `{{variable}}` placeholders above map directly to the "Set Variables" outputs from
Step 9 (Calculate Derived Metrics) in the Universal Report Generator workflow. Keep
naming consistent across all 5 Router branches so Step 10's OpenAI module can reuse
one set of mapped fields regardless of which branch fired.

---

**Last Updated:** 2026-09-08
**Status:** REFERENCE — paste into OpenAI "Create Completion" module per goal branch
