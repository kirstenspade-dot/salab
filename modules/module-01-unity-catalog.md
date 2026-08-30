[← Module 00: Setup](module-00-setup.md)  |  [🏠 Home](../README.md)  |  [Module 02: Genie Agent →](module-02-genie-agent.md)

---

## MODULE 01: Unity Catalog Foundations

**METADATA**
- Time estimate: 25 minutes
- Feature(s) covered: Unity Catalog governance (column comments, grants, lightweight semantic context)
- Depends on: Module 00 (tables must exist)
- Key artifact created: Annotated, governed tables ready for Genie

### Documentation references

Use these Databricks docs for the governance and metadata steps in this module:

* [Unity Catalog setup guide](https://docs.databricks.com/aws/en/data-governance/unity-catalog/setup-uc/)
* [Unity Catalog permissions model concepts](https://docs.databricks.com/aws/en/data-governance/unity-catalog/access-control/permissions-concepts/)
* [Unity Catalog privileges reference](https://docs.databricks.com/aws/en/data-governance/unity-catalog/access-control/privileges-reference/)
* [Add comments to data and AI assets](https://docs.databricks.com/aws/en/comments/index/)
* [Discover data comments in Catalog Explorer](https://docs.databricks.com/aws/en/discover/index/)

**VIDEO SCRIPT (2 minutes, Synthesia, second-person)**

[HOOK — 15 sec] "Right now, if you pointed a general AI chatbot at your sales data, it wouldn't know what 'active customer' means, which table is the source of truth, or who's allowed to see what. That's the problem Unity Catalog solves before Genie ever enters the picture."

[PROBLEM — 30 sec] "Without governance, every AI tool that touches your data either gets too much access — a compliance risk — or has to be manually restricted table by table, which doesn't scale. Homegrown systems often grant agents broad access and rely on prompt engineering to filter results after the fact. That's fragile, and it's exactly the kind of ungoverned setup that produced RAD's inconsistent chatbot answers."

[HOW IT WORKS — 45 sec] "Unity Catalog is the governance and semantic context layer underneath everything in Databricks — tables, AI agents, models, and MCP connections. Think of it like a building's badge system: the badge doesn't change based on which door you're trying to open, it works everywhere consistently. Column comments, object names, and grants are not just admin metadata; they become part of the technical grounding that helps Genie interpret business questions correctly and keeps the resulting answers permission-aware by default."

[WHY IT MATTERS — 20 sec] "This means when we build the Genie Agent in the next module, it inherits governance automatically — no separate security model to build and maintain for AI specifically."

[WHAT YOU'LL BUILD — 10 sec] "In this module, you will annotate RAD's tables so they're ready for Genie to understand. Let's go."

**CONCEPT CALLOUT BOXES**

> **CONCEPT: Column Comments Create a Lightweight Semantic Context Layer**
> Genie uses Unity Catalog column names and descriptions — along with synonyms and example SQL — to generate accurate responses. In the market, you may hear this described as a semantic layer, ontology, or business context layer. For this lab, we are not building a formal knowledge graph; we are showing the lighter-weight pattern that most teams should start with first. A column named `conversion_rate` with no comment is ambiguous; a clear comment removes the guesswork.
> **SE TALKING POINT:** "You do not need a heavyweight knowledge graph to improve AI accuracy — start with a governed semantic context layer in Unity Catalog, then expand only if the use case demands it."

> **CONCEPT: Governance Is Inherited, Not Rebuilt for AI**
> Genie Agents run with the end user's own credentials. The same row filters, column masks, and object privileges that already govern a human analyst's SQL access automatically govern what the AI agent can return to that same person.
> **SE TALKING POINT:** "You're not building a second security model for AI — whatever access controls you already have in Unity Catalog just work, automatically, for every Genie conversation."

**HANDS-ON STEPS**

1. In this step, you will add column comments to RAD_STORE_METRICS because Genie relies directly on this metadata to answer questions correctly.
```sql
ALTER TABLE RAD_CATALOG.RAD_FOUNDATION.RAD_STORE_METRICS
  ALTER COLUMN conversion_rate COMMENT 'Daily foot-traffic-to-purchase conversion rate, calculated once per store per day. Range 0.00-1.00.';

ALTER TABLE RAD_CATALOG.RAD_FOUNDATION.RAD_STORE_METRICS
  ALTER COLUMN avg_basket_size COMMENT 'Average dollar value per transaction at this store on this date, in USD.';
```
UI ALTERNATIVE: In Catalog Explorer, navigate to the table, click "Edit" on the schema tab, and add descriptions directly in the UI — no SQL required.

2. In this step, you will grant RAD_BUSINESS_USER read access to the core sales foundation tables, because Genie Agents cannot return data a user isn't already permitted to see.
```sql
GRANT SELECT ON TABLE RAD_CATALOG.RAD_FOUNDATION.RAD_STORE TO `RAD_BUSINESS_USER`;
GRANT SELECT ON TABLE RAD_CATALOG.RAD_FOUNDATION.RAD_SALES_TXN TO `RAD_BUSINESS_USER`;
GRANT SELECT ON TABLE RAD_CATALOG.RAD_FOUNDATION.RAD_STORE_METRICS TO `RAD_BUSINESS_USER`;
GRANT SELECT ON TABLE RAD_CATALOG.RAD_FOUNDATION.RAD_SKU_LOOKUP TO `RAD_BUSINESS_USER`;
-- Intentionally do NOT grant RAD_PRODUCT_REVIEWS to RAD_BUSINESS_USER yet.
-- We will use that difference later in Module 05 to prove that the same shared
-- Genie Agent still respects Unity Catalog permissions per user.
```

**VALIDATION CHECKPOINT**
```sql
-- VALIDATION: Module 01 — confirms column comments and grants are in place
DESCRIBE TABLE EXTENDED RAD_CATALOG.RAD_FOUNDATION.RAD_STORE_METRICS;
-- Expected: conversion_rate and avg_basket_size show non-null comments in the output
-- If comments show as null, the ALTER COLUMN statements did not run — re-run Step 1.
```

> **NOTE: Governance setup that matters later in Module 05**
> At this point in the lab, the default least-privilege design should be:
> * `RAD_ANALYST` can read all five foundation tables, including `RAD_PRODUCT_REVIEWS`
> * `RAD_BUSINESS_USER` can read the four structured sales tables only
> * `RAD_BUSINESS_USER` does **not** have access to `RAD_PRODUCT_REVIEWS`
>
> That default makes the Module 05 governance proof easy to demonstrate without having to go back and rework earlier grants.

**"WHY THIS WINS" — SE TALKING POINTS**
- **TECHNICAL DIFFERENTIATOR:** Unity Catalog provides one governance plane for both human SQL access and AI-generated access. You are not building and synchronizing a second permission model for the agent.
- **BUSINESS VALUE:** governance stops being a blocker to AI adoption because the same controls that already satisfy data owners now carry forward into conversational analytics.
- **COMPETITIVE POSITIONING:** many competitive AI analytics stories start with the chat experience; Databricks starts with the governed data layer beneath it. That is the stronger enterprise architecture story when security, auditability, and reuse matter.
- **SA SELLING POINT:** before you ever demo the agent, you can explain exactly why its answers are safer and more defensible than an LLM layered over exported data.

---



---

[← Module 00: Setup](module-00-setup.md)  |  [🏠 Home](../README.md)  |  [Module 02: Genie Agent →](module-02-genie-agent.md)
