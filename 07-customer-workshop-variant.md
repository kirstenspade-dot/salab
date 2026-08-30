[← Cleanup](06-cleanup.md)  |  [🏠 Home](README.md)  |  

---

# SECTION 7: Customer Workshop Variant

**Renamed objects:** All `RAD_` prefixes become `WORKSHOP_` (e.g., `WORKSHOP_STORE`, `WORKSHOP_GENIE_SALES`).

**Removed for customer delivery:** all competitive positioning callouts and internal tool references (internal field-training material only).

**Reduced scope (~60% of full lab, ~90 minutes):**
- Module 00: Setup (10 min, mostly pre-run by facilitator)
- Module 01: Unity Catalog Foundations (5 min, condensed to one concept callout instead of two)
- Module 02: Genie Agent (35 min — kept nearly full-length, this is the core "aha" moment for a customer)
- Module 04: MCP Connectors (10 min, conceptual walkthrough rather than full hands-on — most customers won't build this live)
- Module 05: Governance Proof (10 min, facilitator-led persona comparison rather than full hands-on for every participant)
- Module 03 (Notebooks/validation) is **cut entirely** for the customer variant — too technical/internal-facing for a first workshop.

**Industry variable:**
```sql
-- Optional facilitator variable for workshop notes:
-- SET VAR industry = 'Retail';
-- This does not automatically rename tables in the lab; use Section 2e to swap
-- object names and business language manually for the target industry.
```

**Industry substitution table:**

| Original (Retail) | Financial Services | Healthcare |
|---|---|---|
| RAD_STORE | FS_BRANCH | HC_CLINIC |
| RAD_SALES_TXN | FS_TRANSACTION | HC_CLAIM |
| RAD_PRODUCT_REVIEWS | FS_ADVISOR_NOTES | HC_PATIENT_NOTES |

**Facilitator callout boxes:**

> **PAUSE HERE — Ask the customer:** "Where does your team go today when a business leader needs a fast answer from data spread across multiple systems? How long does that usually take?"

> **PAUSE HERE — Ask the customer (after Module 02):** "If this agent were live tomorrow, whose job gets easier first — the business leader asking the question, or the analyst who currently fields these requests manually?"

**Pre-run note:** Facilitator should pre-run all of Module 00 and Module 01 before the customer session begins, so the customer's live time is spent entirely in the Genie Agent experience (Module 02) — the part that actually sells.


---

[← Cleanup](06-cleanup.md)  |  [🏠 Home](README.md)  |  
