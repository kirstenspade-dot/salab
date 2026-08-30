[← Certification Gate](07-grading-certification-gate.md)  |  [🏠 Home](README.md)  |  [Customer Workshop Variant →](09-customer-workshop-variant.md)

---

# SECTION 8: Instructor Guide

**PRE-WORK:**

* Run Module 00 Steps 1–4 before the session if running as a live group session (~10 minutes). Confirm a pro/serverless SQL warehouse exists and is not paused.
* If you want to offer the Section 7 challenge-out path, announce it before the lab begins and set a submission deadline 3-5 business days before the live session or completion deadline.
* Decide on one official submission destination for both challenge-out and standard-path artifacts, such as your LMS assignment, certification tracker, or enablement intake form.
* If your team uses Yoodli, position it as optional rehearsal support or optional recording capture — not as the grading authority.

**REVIEW WORKFLOW FOR SECTION 7:**

1. Review challenge-out submissions before the lab and mark participants as either:
   * `Challenge-out passed`, or
   * `Challenge-out attempted; standard path required`
2. For participants taking the standard path, review their written answers, short walkthrough, and BLUF after lab completion.
3. If an answer looks generic, AI-generated, or disconnected from the participant's own artifacts, require one additional verbal explanation before marking them complete.
4. Use the anti-automation checks in Section 7 consistently; do not waive them just because the technical build appears correct.

**COMMON STUCK POINTS:**
1. Module 02, Step 4 — agent returns "cannot find relevant data" almost always means Module 01 grants weren't applied to the test account. Have participants re-check Module 01 Step 2.
2. Module 03 — participants most often attach the notebook to a SQL warehouse instead of Python-capable notebook compute, or paste only part of the Step 2 code block. Confirm they are using a Python notebook on notebook compute and pasted the full cell.
3. Module 04 — participants often forget to substitute their actual workspace hostname and Agent ID, and try to run the literal `<workspace-hostname>` placeholder.
4. Module 05 — if the second user cannot open the agent or gets no useful result on allowed questions, check two things separately: whether the agent was shared with `CAN RUN`, and whether the user actually has the required Unity Catalog grants on the underlying tables. These are different permission layers.
5. Second-user setup — when a participant adds a second user by email, remind them to check that user's inbox for the Databricks invitation/confirmation email. The second user should sign in to the existing workspace; they do not need to create a brand-new trial workspace for this lab.

**FILL-IN-THE-BLANK TIMING:** Reveal `[XXX]` answers after each module's validation checkpoint, not before — the point is participants reason through the value before confirming it.

**VIDEO TIMING:** Have participants watch each module's video independently before starting hands-on steps, rather than playing it aloud in a group setting — this respects self-paced completion for asynchronous learners.

**TIME BUFFERS:** If running long, Module 04 (MCP) can be demonstrated by the instructor rather than hands-on per participant, since it's largely conceptual once Module 02 is complete. Module 05 can also be run as an instructor-led demo if not every participant has a second user available. If time allows, extend Module 02 by having participants write and test 2 additional example SQL queries of their own.

**RECOMMENDED SCORING APPROACH:**

* Do not score based on tool usage, assistant usage, or whether the participant opened a coaching skill.
* Score based on demonstrated understanding tied to the participant's own artifacts.
* For asynchronous programs, a recorded walkthrough plus written answers is usually sufficient.
* For higher-stakes certification, add a short live verbal defense for borderline cases.

**ACCOUNT SETUP CHECKLIST:**
- [ ] Unity Catalog enabled on the workspace
- [ ] Pro or serverless SQL warehouse available
- [ ] Participant has ACCOUNTADMIN or equivalent workspace admin rights
- [ ] Account-level groups `RAD_ADMIN`, `RAD_ANALYST`, and `RAD_BUSINESS_USER` created
- [ ] Required users added to the appropriate groups
- [ ] Required users and groups assigned to the target workspace
- [ ] Optional second user created if demonstrating persona separation live
- [ ] Notebook compute available for Module 03
- [ ] Genie Agents available in the target workspace

---



---

[← Certification Gate](07-grading-certification-gate.md)  |  [🏠 Home](README.md)  |  [Customer Workshop Variant →](09-customer-workshop-variant.md)
