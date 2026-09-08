# Metrics Mapping by Goal

This document defines which metrics are primary, secondary, and benchmark-worthy for each campaign goal.

---

## TRAFFIC

**Objective:** Drive people to view the property listing

### Primary Metrics
| Metric | What It Means | How to Calculate |
|--------|---------------|------------------|
| **Landing Page Views (LPV)** | People who clicked the ad and landed on listing | Direct from Facebook API |
| **Click-Through Rate (CTR)** | % of people who saw ad and clicked | Clicks ÷ Reach |
| **Cost Per Click (CPC)** | How much each click cost | Spend ÷ Clicks |
| **Cost Per Landing Page View** | How much each listing visit cost | Spend ÷ LPV |

### Secondary Metrics
- Reach (unique people who saw the ad)
- Impressions (total times ad appeared)
- Frequency (average times per person saw the ad)
- Engagement (likes, comments, shares)

### Benchmarks (Australian Real Estate Average)
- CTR: 4.28%
- CPM: $30.09
- CPC: ~$0.24
- Frequency: 1.5–2.5x

### Report Narrative
> "X people reached → X clicks (CTR: Y%) → X landed on listing (Cost: $Z per view)"

### AI Prompt Focus
- Cost efficiency of clicks
- Conversion from click to listing visit
- Quality of traffic (frequency, engagement)

---

## LEAD GENERATION

**Objective:** Capture qualified inquiries/form submissions

### Primary Metrics
| Metric | What It Means | How to Calculate |
|--------|---------------|------------------|
| **Form Submissions** | Leads captured (if tracking integration exists) | CRM/Form integration |
| **Cost Per Lead** | How much each lead cost | Spend ÷ Leads |
| **Lead Conversion Rate** | % of ad engagements that became leads | Leads ÷ Clicks |
| **Click Volume** | Total people engaging with ad | Direct from Facebook API |

### Secondary Metrics
- Reach
- CTR
- Landing Page Views (traffic to listing)
- Quality signals (engagement, time on page if available)

### Benchmarks (Australian Real Estate)
- Lead cost: $5–$25 depending on market
- Conversion rate: 2–5%
- CTR: 4.28%

### Report Narrative
> "X people clicked → X inquiries captured (Cost: $Y per lead, Conversion: Z%)"

### AI Prompt Focus
- Lead quality indicators
- Cost efficiency of lead generation
- Comparison to industry benchmarks
- Recommendations for optimization

---

## AWARENESS

**Objective:** Maximize visibility and reach

### Primary Metrics
| Metric | What It Means | How to Calculate |
|--------|---------------|------------------|
| **Reach** | Unique people who saw the ad | Direct from Facebook API |
| **Impressions** | Total times ad appeared in feeds | Direct from Facebook API |
| **Frequency** | Average times each person saw the ad | Impressions ÷ Reach |
| **Cost Per Reach** | How much to reach one person | Spend ÷ Reach |
| **CPM (Cost Per Thousand)** | How much per 1,000 impressions | (Spend ÷ Impressions) × 1,000 |

### Secondary Metrics
- CTR (if applicable)
- Engagement (comments, shares)
- Video views (if video content used)

### Benchmarks (Australian Real Estate)
- CPM: $30.09 (average)
- Optimal frequency: 1.5–2.5x
- Average CTR: 4.28%

### Report Narrative
> "X unique people saw the property (Z frequency) across Y total impressions. Cost: $A per person."

### AI Prompt Focus
- Reach efficiency and cost per person
- Frequency sweetspot (not too low, not annoying)
- Market positioning (how many people know about property)

---

## SALES (Consideration)

**Objective:** Move toward purchase intent and conversion

### Primary Metrics
| Metric | What It Means | How to Calculate |
|--------|---------------|------------------|
| **Video Plays** | People who watched video content | Direct from Facebook API |
| **Engagement Rate** | % showing buying intent signals | (Engagements ÷ Reach) × 100 |
| **Total Engagements** | Likes, comments, shares, saves, clicks | Direct from Facebook API |
| **Saves** | People who saved the property listing | Direct from Facebook API (if available) |
| **CTR** | Click interest | Clicks ÷ Reach |

### Secondary Metrics
- Reach
- Frequency
- Cost Per Engagement
- Video Completion Rate

### Benchmarks (Australian Real Estate)
- Video play rate: 10–20% of reach
- Engagement rate: 2–5%
- CTR: 4.28%

### Report Narrative
> "X people engaged with content (Y% engagement rate) including X video views and X saves. This signals buying interest."

### AI Prompt Focus
- Engagement quality and intent signals
- Video performance
- Comparison to passive awareness
- What engagement tells about buyer interest

---

## ENGAGEMENT (Direct Interaction)

**Objective:** Drive direct communication (messages, comments, inquiries)

### Primary Metrics
| Metric | What It Means | How to Calculate |
|--------|---------------|------------------|
| **Messages Received** | Direct messages to property/agent | CRM/Messenger integration |
| **Message Rate** | % of people who messaged | Messages ÷ Reach |
| **Comments** | Public comments on ad | Direct from Facebook API |
| **Shares** | People who shared to friends | Direct from Facebook API |
| **Profile Clicks** | People clicked agent/property profile | Direct from Facebook API |
| **Cost Per Message** | How much each message cost | Spend ÷ Messages |

### Secondary Metrics
- Reach
- CTR
- Video plays
- Total engagements
- Landing Page Views

### Benchmarks (Australian Real Estate)
- Message rate: 0.5–2% of reach
- Comment rate: 0.1–0.5%
- Message cost: $5–$20 depending on market

### Report Narrative
> "X people reached → X direct messages (Y% message rate, Cost: $Z per message). This represents qualified, active interest."

### AI Prompt Focus
- Direct communication as strongest intent signal
- Message quality indicators
- Comparison of passive vs. active engagement
- Conversion potential (messagers often become buyers)
- Recommendations for responding to inquiries

---

## Cross-Goal Comparison

| Metric | Traffic | Lead Gen | Awareness | Sales | Engagement |
|--------|---------|----------|-----------|-------|------------|
| **Reach** | Secondary | Secondary | Primary | Secondary | Secondary |
| **CTR** | Primary | Primary | Secondary | Secondary | Secondary |
| **LPV** | Primary | Secondary | — | — | Secondary |
| **Engagement** | Secondary | Secondary | Secondary | Primary | Primary |
| **Video Plays** | — | — | — | Primary | Secondary |
| **Messages** | — | Secondary | — | — | Primary |
| **Conversions** | — | Primary | — | — | Primary |

---

## Implementation in Workflow

When workflow parses goal from ad group name:

```
IF goal == "traffic"
  → Pull: LPV, CTR, CPC, Reach, Frequency
  → Compare: CTR vs 4.28%, CPM vs $30.09
  → AI Prompt: Focus on click-to-listing efficiency
  → Report Template: Traffic-focused layout

IF goal == "lead-generation"
  → Pull: Form submissions, Cost per lead, Clicks, Reach
  → Compare: Cost per lead vs $5–$25 range
  → AI Prompt: Focus on lead quality and cost efficiency
  → Report Template: Lead-focused layout

... (similar for awareness, sales, engagement)
```

---

**Last Updated:** 2026-09-08  
**Status:** REFERENCE DOCUMENT — Use when building report templates
