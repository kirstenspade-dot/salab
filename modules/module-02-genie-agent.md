[← Module 01: Unity Catalog](module-01-unity-catalog.md)  |  [🏠 Home](../README.md)  |  [Module 03: Notebooks →](module-03-notebooks-validate.md)

---

## MODULE 02: Genie Agent — Conversational Analytics

**METADATA**
- Time estimate: 35 minutes
- Feature(s) covered: Genie Agent creation and curation
- Depends on: Module 01 (governed, annotated tables)
- Key artifact created: RAD_GENIE_STORE_SALES, a working Genie Agent

### Documentation references

Use these Databricks docs for creating and curating the agent in this module:

* [Genie Agents overview](https://docs.databricks.com/aws/en/genie-agents/index/)
* [Genie Agents concepts](https://docs.databricks.com/aws/en/genie-agents/concepts/)
* [Create and manage a Genie Agent](https://docs.databricks.com/aws/en/genie-agents/set-up/)
* [Curate an effective Genie Agent](https://docs.databricks.com/aws/en/genie-agents/best-practices/)
* [Genie product landing page](https://docs.databricks.com/aws/en/genie/index/)

**VIDEO (2 minutes, Synthesia)**

[Watch the Module 02 video here](https://share.synthesia.io/c7921d65-ba70-4c70-ab4f-516ea2b4effc) to learn how Genie Agents turn governed Unity Catalog data into a curated conversational analytics experience for business users. This video explains the business problem RAD is solving, why a general chatbot produced inconsistent answers, how a Genie Agent is grounded with scoped tables, business instructions, and example SQL, and why the experience remains read-only, permission-aware, and technically inspectable. By the end of the video, participants should understand what they are about to build: `RAD_GENIE_STORE_SALES`, a governed Genie Agent for store-level sales questions.

**CONCEPT CALLOUT BOXES**

> **CONCEPT: Genie Agents Are Curated, Not Wide Open**
> A Genie Agent is scoped to a deliberate set of tables (up to 30), not your entire catalog. Curation — picking the right tables, writing example SQL, adding instructions — is what separates a reliable agent from a confusing one.
> **SE TALKING POINT:** "We're not pointing AI at everything and hoping — we're scoping it to exactly what this audience needs, the same discipline you'd use scoping a dashboard."

> **CONCEPT: Instructions, Examples, and Benchmarks Make the Agent Defensible**
> Example SQL, plain-language instructions, and benchmark questions are how you encode RAD-specific logic — like which table is the source of truth for "daily sales" — instead of hoping the model infers it. If you already have a validated query or dashboard that represents the business logic well, use it as a trusted asset rather than recreating that logic from scratch.
> **SE TALKING POINT:** "You're not asking a generic model to improvise business logic — you're giving it grounded instructions, known-good examples, and tests."

> **CONCEPT: Generated Queries Are Always Read-Only**
> Genie-generated SQL never writes or modifies data — only reads it. This is a hard product boundary, not a configuration option.
> **SE TALKING POINT:** "There's no scenario where a business user's question accidentally changes your data — Genie physically can't write, only read."

**HANDS-ON STEPS**

1. In this step, you will create the Genie Agent because this is the interface your business user will actually talk to.

UI ALTERNATIVE: This step is UI-first. In the Databricks workspace left nav, go to **Genie Agents** > **New Genie Agent**. Name it `RAD_GENIE_STORE_SALES`.

```
-- [XXX] -- [XXX]: When prompted for a SQL warehouse, select RAD_WH
-- (created in Module 00). You need at least CAN USE permission on it.
```

2. Click **Configure** and complete the agent setup tabs before you test anything.

In **Configure > About**, use the following description:
```
RAD_GENIE_STORE_SALES helps retail sales leaders analyze store performance, product performance, and regional trends using governed Unity Catalog data. Use RAD_STORE_METRICS as the source of truth for finance-approved daily sales metrics, and use RAD_SALES_TXN for transaction-level detail when the question is about product, SKU, or channel behavior.
```

In **Configure > About**, replace or add starter questions such as:
```
Which Southeast stores had the highest average basket size during the sample week of 2026-07-01 through 2026-07-07?
What was total skincare sales in the Southeast region during the sample week of 2026-07-01 through 2026-07-07?
Which store has the highest conversion rate in the sample data?
Which stores are larger than 5,000 square feet and how are they performing?
How many stores do we have by region?
```

3. In **Configure > Sources**, add these four tables to the agent's scope. Leave `RAD_PRODUCT_REVIEWS` out for now.

* `RAD_CATALOG.RAD_FOUNDATION.RAD_STORE`
* `RAD_CATALOG.RAD_FOUNDATION.RAD_SALES_TXN`
* `RAD_CATALOG.RAD_FOUNDATION.RAD_STORE_METRICS`
* `RAD_CATALOG.RAD_FOUNDATION.RAD_SKU_LOOKUP`

> **CONCEPT: Scope Deliberately**
> We're leaving RAD_PRODUCT_REVIEWS out of this first agent version on purpose — mixing structured sales data and unstructured review text in one agent, on day one, is how agents get confusing fast. Best practice is to start minimal and expand deliberately.
> **SE TALKING POINT:** "Start narrow, prove value, then expand — the same POC discipline you'd use with any new platform capability."

4. In **Configure > Instructions**, add plain-language guidance that teaches the agent your business rules.

Copy this instruction text:
```
Use RAD_STORE_METRICS.daily_sales as the source of truth when the user asks about sales performance, daily sales, or store performance. Do not recalculate finance-approved daily sales by summing RAD_SALES_TXN.sale_amount unless the question is explicitly about transaction-level detail, SKU mix, or sales by channel. Use RAD_STORE to answer questions about region, state, store name, store age, or square footage. Join RAD_SALES_TXN to RAD_SKU_LOOKUP when the question references product category, product name, brand, or SKU.
```

5. In **Configure > Examples**, add at least these three example SQL queries.

Optional small uplift: if you already have a validated saved query or dashboard widget that represents one of these business questions well, add it as a trusted asset instead of recreating the same logic manually. Skip this in a time-constrained session if it would slow the lab down.

```sql
-- Example 1: Southeast region skincare sales during the sample week
SELECT s.store_name, SUM(t.sale_amount) AS total_sales
FROM RAD_CATALOG.RAD_FOUNDATION.RAD_SALES_TXN t
JOIN RAD_CATALOG.RAD_FOUNDATION.RAD_STORE s
  ON t.store_id = s.store_id
JOIN RAD_CATALOG.RAD_FOUNDATION.RAD_SKU_LOOKUP sk
  ON t.sku_id = sk.sku_id
WHERE s.region = 'Southeast'
  AND sk.category = 'skincare'
  AND t.sale_date BETWEEN DATE '2026-07-01' AND DATE '2026-07-07'
GROUP BY s.store_name
ORDER BY total_sales DESC;

-- Example 2: Highest average basket size for Southeast stores
SELECT s.store_name, m.avg_basket_size
FROM RAD_CATALOG.RAD_FOUNDATION.RAD_STORE_METRICS m
JOIN RAD_CATALOG.RAD_FOUNDATION.RAD_STORE s
  ON m.store_id = s.store_id
WHERE s.region = 'Southeast'
  AND m.metric_date BETWEEN DATE '2026-07-01' AND DATE '2026-07-07'
ORDER BY m.avg_basket_size DESC;

-- Example 3: Store conversion leaderboard
SELECT s.store_name, s.region, m.conversion_rate
FROM RAD_CATALOG.RAD_FOUNDATION.RAD_STORE_METRICS m
JOIN RAD_CATALOG.RAD_FOUNDATION.RAD_STORE s
  ON m.store_id = s.store_id
ORDER BY m.conversion_rate DESC;
```

6. In this step, you will test the agent with a real question, because an untested agent is not a shipped agent.

Ask the agent: *"Which Southeast stores had the highest average basket size during the sample week of 2026-07-01 through 2026-07-07?"*

Expected answer: `Radiance Buckhead` should rank above `Radiance Dilworth`. In the sample data, `Radiance Buckhead` (`RAD-1001`) has an `avg_basket_size` of `40.60`, and `Radiance Dilworth` (`RAD-1005`) has an `avg_basket_size` of `33.10`.

**VALIDATION CHECKPOINT**
```
-- VALIDATION: Module 02 — confirms the Genie Agent returns a grounded answer
-- Ask RAD_GENIE_STORE_SALES: "Which Southeast stores had the highest average
-- basket size during the sample week of 2026-07-01 through 2026-07-07?"
-- Expected: Genie returns a ranked list of Southeast stores with
-- Radiance Buckhead (RAD-1001) first at 40.60 and Radiance Dilworth
-- (RAD-1005) second at 33.10, generated from a visible/inspectable SQL query.
-- Because the sample metrics table contains one snapshot date in that range,
-- the answer should reflect those exact values rather than a multi-day average.
-- If Genie responds it cannot find relevant data, confirm Module 01 grants
-- were applied to the account you're testing as.
```

**"WHY THIS WINS" — SE TALKING POINTS**
- **TECHNICAL DIFFERENTIATOR:** Genie gives you native natural-language-to-SQL over governed data without building and maintaining your own orchestration, permission shim, or prompt stack.
- **BUSINESS VALUE:** It collapses a multi-day analytics request cycle into a self-serve experience the sales leader can actually use, while keeping the data team in control of meaning and access.
- **COMPETITIVE POSITIONING (customer exploring Snowflake):**
  - *Objection you may hear:* "Snowflake's conversational AI experience has a simpler, more unified UI."
  - *Response:* UI simplicity matters, but enterprise buyers also ask how the experience is grounded, governed, and validated. Genie inherits Unity Catalog controls directly and exposes the generated SQL, which makes the answer path easier to inspect and defend.
  - *Objection you may hear:* "Snowflake is more open — we're not locked into one platform."
  - *Response:* Unity Catalog works with open formats and cross-platform data architectures, so the differentiator here is not lock-in; it is governed interoperability plus a stronger validation story.
  - [DOC NEEDED: pull current, specific Snowflake Cortex Analyst / Intelligence documentation directly before using this competitive framing live, so the objection response reflects Snowflake's current, real capabilities rather than an assumption.]
- **SA SELLING POINT:** the strongest demo is not "ask a cool question." It is "ask a cool question, inspect the SQL, show the source-of-truth instruction, and then show the benchmark that proves the agent answers it reliably."

---



---

[← Module 01: Unity Catalog](module-01-unity-catalog.md)  |  [🏠 Home](../README.md)  |  [Module 03: Notebooks →](module-03-notebooks-validate.md)
