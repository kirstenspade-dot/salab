  |  [🏠 Home](README.md)  |  [Company Profile →](02-company-profile.md)

---

# SECTION 1: Lab Identity Card

**Lab Title:** Build a Governed Conversational Analytics Experience with Databricks Genie

**Lab Code:** ENBL-RAD-GENIE-HOL-001

**Tagline:** By the end of this lab, you will have built a governed, testable conversational analytics experience with Databricks Genie: a retail sales leader can ask store-level questions in plain English and get answers grounded in Unity Catalog, backed by inspectable SQL, and validated through repeatable benchmarks rather than AI guesswork.

**Total Duration:** ~2 hours 5 minutes (125 minutes)
**Module Count:** 5 core modules + Setup (Module 00) + Cleanup

**Target Audience:** Solutions Architects / Sales Engineers, all seniority levels. No prior Databricks experience required; basic SQL familiarity recommended.

**Prerequisites:**
- Databricks workspace with Unity Catalog enabled (trial or company sandbox workspace)
- Workspace admin access. If your workspace is identity-federated, workspace admins can usually create the required account-level groups; otherwise have an account admin create `RAD_ADMIN`, `RAD_ANALYST`, and `RAD_BUSINESS_USER` before the session.
- Optional but recommended: a second test user if you want to demonstrate the `RAD_BUSINESS_USER` persona separately from the admin/analyst persona. A single-user fallback is supported for self-paced labs.
- A pro or serverless SQL warehouse available in the workspace
- Notebook compute available for Module 03, such as serverless notebook compute or a cluster that supports Python

**Account/Environment Requirements — Explicit Exclusions:**
- This lab does NOT require access to a real customer environment or real customer data.
- This lab does NOT require BigQuery or SAS Viya access — those systems appear only in the narrative as the "before" state the fictional company is moving away from.
- This lab does NOT require production MCP client credentials — the MCP module uses the documented Genie MCP URL pattern in a sandbox context only.

**Final Artifact Statement:** By the end of this lab, you will have built a Unity Catalog–governed data foundation, configured a curated Genie Agent over that data, validated it through both a Python API smoke test and native benchmark evaluations, demonstrated persona-specific governance behavior with a second user or analyst/business-user split, and exposed it through the documented Genie MCP endpoint pattern so the same governed experience could be embedded in Slack, Teams, or a custom app.

**Time-Per-Module Table:**

| Module | Title | Time |
|---|---|---|
| 00 | Setup | 15 min |
| 01 | Unity Catalog Foundations | 25 min |
| 02 | Genie Agent — Conversational Analytics | 35 min |
| 03 | Databricks Notebooks — Validate & Iterate | 25 min |
| 04 | MCP Connectors — Expose Genie Beyond the Chat UI | 5 min |
| 05 | Governance Proof — Show the Same Agent Behaves Differently by User | 15 min |
| — | Cleanup | 5 min |

---



---

  |  [🏠 Home](README.md)  |  [Company Profile →](02-company-profile.md)
