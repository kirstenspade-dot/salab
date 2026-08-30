[← Company Profile](02-company-profile.md)  |  [🏠 Home](README.md)  |  [Module 00: Setup →](modules/module-00-setup.md)

---

# SECTION 3: Narrative Arc

Today, a RAD regional sales leader who wants to know how the Southeast region's skincare category performed last week has to file a request with the analytics team and wait — because the answer requires joining data that lives in BigQuery, Databricks, and SAS Viya, and only the analytics team knows how to do that safely. When RAD's ops team tried to shortcut this with a general-purpose AI chatbot pointed at exported data, they got answers that sounded confident but couldn't be trusted — no governance, no consistent source of truth, no way to know if the numbers were even current.

This lab rebuilds that experience the right way. First, we establish a governed foundation in Unity Catalog so the data, permissions, and business meaning are explicit before AI ever enters the picture. Then we configure a Genie Agent on top of that foundation: a curated natural-language interface with scoped tables, business instructions, and example SQL, so the agent is grounded rather than generic. Next, we validate it the way a serious data team would: a Python notebook proves API-level connectivity and inspectable responses, while benchmark evaluations prove repeatable accuracy over time. Finally, we expose the same governed agent through Databricks' documented MCP endpoint, showing how one governed experience can extend beyond the Databricks chat UI into business tools without rebuilding logic or security.

By the end, a RAD sales leader can ask "how did our new skincare line do in the Southeast last week" in plain English and get a trustworthy answer backed by governed data, inspectable SQL, and a validation story an architect can defend in front of both technical and business stakeholders.

**Module Dependency Map:**

```
Module 00: Setup
     │
     ▼
Module 01: Unity Catalog Foundations
     │  (establish governed data, permissions, and semantic context)
     ▼
Module 02: Genie Agent — Conversational Analytics
     │  (curate the agent with scoped tables, instructions, and examples)
     ▼
Module 03: Databricks Notebooks — Validate & Iterate
     │  (prove API behavior, inspect SQL, and run benchmark evaluations)
     ▼
Module 04: MCP Connectors — Expose Genie Beyond the Chat UI
     │  (extend the same governed agent into external tools)
     ▼
Cleanup
```

**SA value-prop throughline:**
* Module 01 proves Databricks starts with governance, not a chatbot prompt.
* Module 02 proves the experience is curated and business-aware, not wide open and unpredictable.
* Module 03 proves the agent is testable and measurable, not just demo-friendly.
* Module 04 proves the governed experience is extensible, not trapped in one UI.

---



---

[← Company Profile](02-company-profile.md)  |  [🏠 Home](README.md)  |  [Module 00: Setup →](modules/module-00-setup.md)
