[← Module 03: Notebooks](module-03-notebooks-validate.md)  |  [🏠 Home](../README.md)  |  [Module 05: Governance Proof →](module-05-governance-proof.md)

---

## MODULE 04: MCP Connectors — Expose Genie Beyond the Chat UI

**METADATA**
- Time estimate: 15 minutes
- Feature(s) covered: Genie MCP endpoint
- Depends on: Module 02/03 (a validated Genie Agent)
- Key artifact created: A documented, callable MCP URL for the agent

### Documentation references

Use these Databricks docs for the MCP connection pattern in this module:

* [Genie MCP server](https://docs.databricks.com/aws/en/agents/mcp-tools/genie-mcp/)
* [Databricks managed MCP servers](https://docs.databricks.com/aws/en/agents/mcp-tools/managed-mcp/)
* [Genie Agents Conversation API](https://docs.databricks.com/aws/en/genie-agents/conversation-api/)

**VIDEO (2 minutes, Synthesia)**

[Module 04 video](https://share.synthesia.io/11fc5678-f004-4336-914f-4953dfa6f7c8) explains how Databricks exposes the same governed Genie Agent beyond the native chat UI through a documented MCP endpoint. This video explains why adoption increases when business users can reach the same agent from tools they already use, how the MCP URL works as a standard connection point instead of a custom integration, and why Unity Catalog governance still applies no matter where the agent is invoked. By the end of the video, participants should understand what they are about to build: a documented MCP URL for `RAD_GENIE_STORE_SALES` that can be used by an MCP-compatible client or internal application.

**CONCEPT CALLOUT BOXES**

> **CONCEPT: MCP Connects Without Rebuilding Governance**
> The Genie MCP URL pattern is `https://<workspace-hostname>/api/2.0/mcp/genie/{genie_agent_id}`. Any MCP-compatible client that calls this URL gets the same governed, permission-aware agent — nothing about security is reconfigured per integration. In Databricks terminology, this is the Genie MCP surface over the same governed semantic context used by the agent.
> **SE TALKING POINT:** "You configure governance once, in Unity Catalog — every new surface you expose Genie through, whether it's chat, an app, or Slack, inherits that same governance automatically."

**HANDS-ON STEPS**

1. In this step, you will construct your agent's MCP URL, because this is the connection point external MCP-compatible tools will use.

```text
https://<workspace-hostname>/api/2.0/mcp/genie/{genie_agent_id}
```

To build it correctly:

* Replace `<workspace-hostname>` with your actual Databricks workspace hostname.
* Replace `{genie_agent_id}` with the Agent ID for `RAD_GENIE_STORE_SALES` from Module 02.
* Keep the rest of the path exactly as written.

> **NOTE: Where do I run this?**
> For this lab, you do **not** run this URL inside a SQL query or Python notebook cell. Module 04 is primarily a documentation and configuration exercise: you construct the MCP URL, verify that it is correctly formed, and understand what an external client would use later.
>
> If you want to test it outside the scope of this lab, you would paste this URL into an **MCP-compatible client or agent configuration** that is authenticated to your Databricks workspace. You are not expected to build that external client integration live in this workshop.

2. In this step, you will identify the two values you need before sharing or configuring the endpoint.

* Find the workspace hostname from your browser URL when you are in Databricks.
* Find the Agent ID in `RAD_GENIE_STORE_SALES` under **Configure > About this agent**.

3. In this step, you will note the permission requirement for any app or service using this endpoint, because MCP access still respects Unity Catalog permissions underneath.

```text
Any service principal or user calling this endpoint needs, at minimum:
USE CATALOG, USE SCHEMA, and SELECT on the underlying Unity Catalog tables or views
that the Genie Agent queries — the same grants from Module 01, not a new permission model.
```

> **CALLOUT: MCP does not bypass governance**
> The MCP URL is only a new access surface, not a new security model. If the caller does not already have access to the underlying Unity Catalog data, the MCP-exposed Genie Agent will not be able to return that data either.

4. In this step, you will document the finished MCP URL in your workshop notes, because the main deliverable of this module is a correctly formed endpoint pattern and a clear explanation of how it would be used.

**VALIDATION CHECKPOINT**
```text
VALIDATION: Module 04 — confirms the MCP URL is correctly formed
Check: does your URL match the pattern
https://<workspace-hostname>/api/2.0/mcp/genie/{genie_agent_id}
with your real hostname and Agent ID substituted in?
If unsure of your Agent ID, find it in Configure > About this agent.
If you are unsure where this URL is used, remember: it is configured in an external
MCP-compatible client, not run directly as a notebook command or SQL statement in this lab.
```

**"WHY THIS WINS" — SE TALKING POINTS**
- **TECHNICAL DIFFERENTIATOR:** One standard, documented MCP endpoint per agent — not a custom integration per destination tool.
- **BUSINESS VALUE:** Meets business users where they already work (Slack, Teams, internal apps), increasing real adoption over a standalone chat tool nobody remembers to open.
- **COMPETITIVE POSITIONING (customer exploring Snowflake):**
  - *Objection you may hear:* "We'd have to build and maintain our own orchestration layer either way."
  - *Response:* With Databricks, the MCP endpoint is pre-configured per Genie Agent — there's no separate orchestration layer to stand up; you're pointing existing tools at a URL that already exists and already inherits governance.

---



---

[← Module 03: Notebooks](module-03-notebooks-validate.md)  |  [🏠 Home](../README.md)  |  [Module 05: Governance Proof →](module-05-governance-proof.md)
