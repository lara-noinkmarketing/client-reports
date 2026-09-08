# Standard Client Reporting System — Documentation

This folder documents the **template-based, multi-client reporting system**
used to onboard and report on all standard real estate clients (i.e. **not**
Kent — see doc 01).

## Read In This Order

| # | Doc | What It Covers |
|---|-----|-----------------|
| 01 | [Kent Isolation Policy](01_KENT_ISOLATION_POLICY.md) | Hard rule — Kent's workflows/sheets are never touched |
| 02 | [Ad Group Naming Convention](02_AD_GROUP_NAMING_CONVENTION.md) | The locked `property--suburb--goal` format and regex |
| 03 | [Metrics Mapping by Goal](03_METRICS_MAPPING_BY_GOAL.md) | What to measure for each of the 5 campaign goals |
| 04 | [System Architecture](04_SYSTEM_ARCHITECTURE.md) | Full data flow, diagrams, component overview |
| 05 | [Master Clients Sheet Template](05_MASTER_CLIENTS_SHEET_TEMPLATE.md) | The one sheet listing every client's config |
| 06 | [Client Tracker Sheet Template](06_CLIENT_TRACKER_SHEET_TEMPLATE.md) | Per-client sheet tracking active ad groups |
| 07 | [Universal Report Generator Workflow](07_UNIVERSAL_REPORT_GENERATOR_WORKFLOW.md) | Make blueprint — the one workflow for all clients |
| 08 | [Goal-Specific AI Prompts](08_GOAL_SPECIFIC_AI_PROMPTS.md) | OpenAI prompt per goal, shared output contract |
| 09 | [Report HTML Templates](09_REPORT_HTML_TEMPLATES.md) | KPI card / hero-stat variants per goal |
| 10 | [Client Provisioning Workflow](10_CLIENT_PROVISIONING_WORKFLOW.md) | Make blueprint — automates new-client setup |
| 11 | [New Client Onboarding Guide](11_NEW_CLIENT_ONBOARDING_GUIDE.md) | The practical checklist to actually run |

## The Short Version

1. **Kent = untouchable**, separate system, referenced for ideas only (doc 01)
2. **One Make workflow** reports on every client, driven entirely by two Google Sheets — no per-client scenario duplication (docs 04, 07)
3. **Properties = Facebook ad groups**, named `property--suburb--goal`, so campaign goal is self-declared and requires zero manual tracking (doc 02)
4. **Report content adapts automatically** to the ad group's goal — traffic, lead-generation, awareness, sales, or engagement (docs 03, 08, 09)
5. **Onboarding a new client** = filling in 6 values + adding property rows, ~15-20 minutes (docs 10, 11)

## Still To Do (Build Phase)

These docs are the **blueprint**. The actual Make scenarios still need to be
built by hand in Make using this documentation as the spec:

- [ ] Build `Universal Report Generator — Standard Clients` scenario (doc 07)
- [ ] Build `Client Provisioning — Standard Clients` scenario (doc 10)
- [ ] Create the blank Client Tracker Sheet template to copy from (doc 06)
- [ ] Create the Master Clients Sheet and migrate Bronwyn's existing config into it (doc 05)
- [ ] Test end-to-end against Bronwyn's existing campaign before calling it live

---

**Last Updated:** 2026-09-08
