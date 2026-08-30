[← Module 04: MCP Connectors](module-04-mcp-connectors.md)  |  [🏠 Home](../README.md)  |  [Cleanup →](../06-cleanup.md)

---

# SECTION 5: Governance Proof — Show the Same Agent Behaves Differently by User

**METADATA**
- Time estimate: 15 minutes
- Feature(s) covered: Genie Agent sharing, Unity Catalog-enforced data access, persona-based validation
- Depends on: Modules 01-04
- Key artifact created: A concrete governance proof showing what the business user can do versus what the analyst can do with the same agent

### Documentation references

Use these Databricks docs for the governance proof in this module:

* [Genie Agent concepts — how data access works](https://docs.databricks.com/aws/en/genie-agents/concepts/)
* [Create and manage a Genie Agent](https://docs.databricks.com/aws/en/genie-agents/set-up/)
* [Row filters and column masks in Unity Catalog](https://docs.databricks.com/aws/en/data-governance/unity-catalog/filters-and-masks/index/)

**VIDEO (2 minutes, Synthesia)**

[Insert the Module 05 video link here](#) to learn how to prove that Genie governance is not just something you configured once and hoped would hold. This video shows that the same Genie Agent can be shared across users while still enforcing Unity Catalog access as the current user, not the agent author. By the end of the video, participants should understand the strongest demo outcome in this lab: one shared agent, different personas, different allowed behaviors, and the same governed rules no matter where the question is asked.

**CONCEPT CALLOUT BOXES**

> **CONCEPT: Same agent, different user, different result**
> A Genie Agent can be shared broadly, but the data returned is still evaluated against the current user's Unity Catalog permissions. The SQL warehouse access can be embedded by the agent author, but the data access is not "borrowed" from the author.
> **SE TALKING POINT:** "This is the governance proof customers actually care about: not that an admin can build a good agent, but that a business user can safely use the same agent without inheriting admin-level data access."

> **CONCEPT: Two permission layers are in play**
> To use the agent, a user needs both access to the **agent itself** and access to the **underlying Unity Catalog data**. Sharing the agent with `CAN RUN` is necessary, but it is not sufficient on its own; the user must also have the right Unity Catalog privileges on the data the agent queries.
> **SE TALKING POINT:** "Agent sharing controls who can open and run the experience. Unity Catalog controls what data that experience can actually return."

> **CONCEPT: Users do not need direct warehouse permissions to ask questions**
> Genie Agents use embedded compute credentials for the SQL warehouse, so a business user does not need separate warehouse-level access just to run the agent. What remains user-specific is the data authorization layer enforced by Unity Catalog.
> **SE TALKING POINT:** "This keeps the consumption model simple for end users without weakening governance."

**HANDS-ON STEPS**

1. Share `RAD_GENIE_STORE_SALES` with the persona you want to use for the governance proof.

   Preferred option for a live demo:
   * share the agent with the second test user you added, or with the `RAD_BUSINESS_USER` group, at **CAN RUN**

   Alternative option if you do not have a second login:
   * use the **analyst** persona to demonstrate authoring rights, then describe the business-user experience conceptually
   * this is weaker than a second-user demo, so prefer the separate user when available

   Exact steps:
   1. Open **Genie Agents** in the left navigation.
   2. Open `RAD_GENIE_STORE_SALES`.
   3. In the upper-right corner, click **Share**.
   4. In the share dialog, search for either:
      * the second user's email address, or
      * the `RAD_BUSINESS_USER` group
   5. Add that user or group.
   6. Set permission to **CAN RUN**.
   7. Click **Save** or **Done**.
   8. Copy the agent link if you want to send the exact URL to the second user.

> **NOTE: Where do I "put" the agent?**
> For this lab, do not overcomplicate asset placement. Keep the agent where it was created and share it directly from the agent's **Share** dialog. New Genie Agents are saved in your user folder by default, and direct sharing is the simplest way to prove the governance story in a workshop.

2. Verify that the second user or business-user persona has the minimum access needed to consume the agent.

   What you are checking:
   * workspace access and the appropriate entitlement to use Genie
   * membership in `RAD_BUSINESS_USER` if you are demonstrating group-based sharing
   * `USE CATALOG`, `USE SCHEMA`, and `SELECT` on the allowed Unity Catalog tables from Module 01
   * `CAN RUN` or higher on `RAD_GENIE_STORE_SALES`

   Exact checks:
   1. Go to **Settings** > **Identity and access** > **Users**.
   2. Confirm the second user appears in the workspace user list.
   3. If you are using group-based access, go to **Settings** > **Identity and access** > **Groups** and confirm the user is a member of `RAD_BUSINESS_USER`.
   4. Reopen `RAD_GENIE_STORE_SALES` and confirm the user or group appears in the **Share** dialog with **CAN RUN** or higher.
   5. If you want to double-check the Unity Catalog side, return to your SQL setup query and confirm Module 00 and Module 01 were already run successfully:

```sql
SHOW GRANTS ON CATALOG RAD_CATALOG;
SHOW GRANTS ON SCHEMA RAD_CATALOG.RAD_FOUNDATION;
SHOW GRANTS ON TABLE RAD_CATALOG.RAD_FOUNDATION.RAD_STORE;
SHOW GRANTS ON TABLE RAD_CATALOG.RAD_FOUNDATION.RAD_SALES_TXN;
SHOW GRANTS ON TABLE RAD_CATALOG.RAD_FOUNDATION.RAD_STORE_METRICS;
SHOW GRANTS ON TABLE RAD_CATALOG.RAD_FOUNDATION.RAD_SKU_LOOKUP;
```

   6. Confirm `RAD_BUSINESS_USER` has the expected `USE CATALOG`, `USE SCHEMA`, and `SELECT` privileges on the four core sales tables.

> **NOTE: If you added a second user by email**
> Databricks typically sends that person a confirmation or invitation email after you add them to the workspace. They do **not** need to start a separate free trial workspace just to participate in this lab. The intended flow is for them to sign in to the workspace you added them to, using that email-based access path.

3. Sign in as the business user in a separate browser profile, incognito window, or different browser.

   Exact steps:
   1. Open a second browser session that is not already signed in as you.
   2. Open the shared Genie Agent link, or log in to the workspace and navigate to **Genie Agents**.
   3. Open `RAD_GENIE_STORE_SALES`.
   4. Ask one allowed business question, such as:
      * "What was total skincare sales in the Southeast region during the sample week of 2026-07-01 through 2026-07-07?"
   5. Wait for the answer and inspect the response.

   Expected result:
   * the business user can open the shared agent and get a grounded answer
   * they do not need direct SQL warehouse permissions to ask the question

4. Compare what the analyst can do versus what the business user can do in the same agent experience.

   As the **business user**, verify these behaviors:
   1. The user can ask questions.
   2. The user can view the answer, chart, and generated result table.
   3. The user does **not** see the same authoring controls used to configure the agent.

   As the **analyst**, verify these behaviors in your original session:
   1. Open the same agent.
   2. Click **Configure**.
   3. Confirm you can edit:
      * instructions
      * trusted SQL examples
      * scoped sources
   4. Open the benchmark/evaluation area and confirm you can inspect results and continue tuning the agent.

> **CALLOUT: This is already governance, even before row filters**
> Many workshops stop after a successful answer. This module goes one step further by proving operational separation of duties: the analyst authors and tunes the agent, while the business user consumes it safely.

5. Optional advanced proof: demonstrate a data-access difference, not just an authoring difference.

   If you want the strongest possible governance demo, do this as the analyst:
   1. Open `RAD_GENIE_STORE_SALES`.
   2. Click **Configure**.
   3. Open **Sources**.
   4. Add `RAD_CATALOG.RAD_FOUNDATION.RAD_PRODUCT_REVIEWS` to the agent.
   5. Save the updated source list.
   6. Do **not** grant `SELECT` on `RAD_PRODUCT_REVIEWS` to `RAD_BUSINESS_USER`.
   7. Confirm the analyst persona already has `SELECT` on `RAD_PRODUCT_REVIEWS` from the earlier lab setup.

   Then test the difference:
   1. As the analyst, ask:
      * "Summarize the top skincare review themes in the Southeast."
   2. Observe that the analyst can get an answer grounded in review data.
   3. Switch to the business-user session.
   4. Ask the same review-related question.
   5. Observe that the business user gets an empty or constrained response because Unity Catalog is enforcing their access at query time.

> **NOTE: Why this optional step is so powerful**
> This is the cleanest way to show that the same shared agent does not flatten permissions. The difference is not prompt wording, not a hidden setting, and not manual branching logic — it is Unity Catalog enforcing the user's own identity.

**VALIDATION CHECKPOINT**
```text
VALIDATION: Module 05 — confirms that Genie governance is observable, not just configured
Check:
1. Can the business user open and run the shared agent?
2. Can the business user get answers to allowed sales questions without direct warehouse permissions?
3. Can the business user avoid seeing authoring controls the analyst uses?
4. If you ran the optional advanced proof, did the analyst succeed on review-data questions while the business user did not?
```

**"WHY THIS WINS" — SE TALKING POINTS**
- **TECHNICAL DIFFERENTIATOR:** Proves that agent sharing and data access are separate layers, with Unity Catalog enforcing data visibility per user at query time.
- **BUSINESS VALUE:** Gives business users self-service access without granting them broad authoring or data privileges they should not have.
- **COMPETITIVE POSITIONING:** This is stronger than saying "trust us, it is governed." You are demonstrating governance live with two personas against the same agent.
- **PRESENTATION PAYOFF:** This is the best ending for the lab because it ties together setup, curation, evaluation, and MCP into one customer-relevant proof: the same agent is useful, testable, shareable, and still controlled.

---



---

[← Module 04: MCP Connectors](module-04-mcp-connectors.md)  |  [🏠 Home](../README.md)  |  [Cleanup →](../06-cleanup.md)
