[← Cleanup](06-cleanup.md)  |  [🏠 Home](README.md)  |  [Instructor Guide →](08-instructor-guide.md)

---

# SECTION 7: Enablement Certification Gate (Three-Layer Model)

**Purpose:** This section defines how enablement should verify that every SA can explain and defend the concepts in this lab with a customer. The design goal is twofold:

* ensure every SA can speak clearly about Unity Catalog governance, Genie Agent curation, benchmark evaluation, MCP exposure, and persona-based access behavior
* prevent a copilot, assistant, or other AI tool from completing the lab end-to-end on the participant's behalf without the participant demonstrating real understanding

**Recommended operating model:** use a **three-layer enablement model** instead of relying on lab completion alone.

## Layer 1 — Challenge-out path (before the lab starts)

Use this for SAs who already know the material and should not be forced through the full lab just to prove it.

This path should happen **before** the lab begins, not after. Recommended timing:

* announce the challenge-out option when the lab is assigned
* require submissions 3-5 business days before the live session or completion deadline
* review submissions before granting a skip on the hands-on lab

A participant may skip the full lab only if they successfully complete all three of the following:

1. **Answer the five gate questions in their own words**
   * The participant answers the five gate questions below without relying on generic AI language.
   * Answers must reference the actual concepts in this lab.

2. **Defend one governance proof live or by recording**
   * The participant shows or narrates one concrete governance proof using the same pattern as Module 05.
   * Acceptable proof includes a live demo, a short screen recording, or a narrated GIF/video showing what the analyst can do versus what the business user can do.

3. **Reference a real artifact, not a hypothetical one**
   * The participant must point to a real agent, real tables, real benchmark output, or a real configuration they created or reviewed.
   * The explanation must use their own environment details, such as the actual table names, the actual scope decision, or the actual benchmark mismatch they observed.

**Where submissions go:** The enablement team should choose one authoritative destination for submissions and reviews, such as the team's LMS assignment, certification tracker, or enablement intake form. Do **not** split the official submission across multiple systems.

If the participant cannot do all three, they should complete the standard lab path below.

> **NOTE: Why this is the right challenge-out bar**
> The goal is not seat time. The goal is verified field readiness. If someone can already explain the value, defend the governance model, and interpret benchmark behavior using real artifacts, that is stronger evidence than forcing them to click through every setup step.

## Layer 2 — Standard completion path

Use this for the default enablement flow.

The participant completes the lab modules, but lab completion by itself is **not** the pass condition. To pass, the participant must also submit the following three deliverables to the same official destination used by enablement for challenge-out review:

1. **Written responses to the five gate questions**
   * Answers must be written in the participant's own words.
   * Answers must refer to the participant's own build, not a generic summary.

2. **A short final walkthrough**
   * 2-3 minutes live, recorded, or submitted as a narrated GIF/video.
   * The walkthrough should show:
     * the Genie Agent
     * one benchmark result or evaluation insight
     * one governance proof from Module 05
     * one statement about why MCP matters for business adoption

3. **A bottom-line-up-front summary**
   * The participant gives a short executive-style summary that starts with the outcome first.
   * Example pattern: governed, testable, shareable, and business-ready.

> **CALLOUT: Why this is stronger than "lab done = pass"**
> A participant can sometimes follow setup steps mechanically. They are much less likely to succeed by accident when they must explain the result, defend the architecture, and connect it to customer value.

## Layer 3 — Coaching support, not scoring authority

A coaching assistant or skill can still be useful, but it should be treated as a **practice layer**, not the system of record for certification.

Recommended use:

* the assistant asks the five gate questions one at a time
* the participant answers in free text
* the assistant challenges vague or incomplete answers
* the assistant gives hints or coaching when the answer is weak
* the participant revises until the answer is solid

Not recommended:

* counting assistant usage as proof of learning
* treating successful assistant interaction as the pass/fail record
* allowing the assistant to auto-complete the gate on behalf of the participant

> **NOTE: How Yoodli fits, if your team uses it**
> Yoodli can be useful as an optional practice surface or as a place to capture a short spoken walkthrough artifact. It should **not** be the grading authority by itself. The authoritative pass/fail signal should still come from human-reviewed artifacts submitted through the enablement team's official tracking system.

## Tracking and completion recommendation

For enablement operations, track completion using these statuses:

* **Challenge-out passed** — participant demonstrated readiness before the lab and was excused from the hands-on path
* **Challenge-out attempted; standard path required** — participant tried to skip the lab but did not meet the bar
* **Standard path passed** — participant completed the lab and passed the certification gate
* **Needs coaching** — participant completed activity but did not yet demonstrate customer-ready understanding

Avoid using "opened the skill," "ran the lab," or "clicked through the modules" as the completion metric. Those actions measure activity, not competence.

## Gate questions (required for both paths)

**Gate 1 (end of Module 01):** *"In your own words, explain why Genie doesn't need a separate security model from the one you already have in Unity Catalog. Give a one-sentence example of what would go wrong if it did."*
* Instructor note: A correct answer references credential/permission inheritance specifically, not just "it's secure." An answer that only restates the concept generically, without the "what would go wrong" example, should be sent back for revision.

**Gate 2 (end of Module 02):** *"You scoped RAD_GENIE_STORE_SALES to 4 tables and deliberately left RAD_PRODUCT_REVIEWS out. Explain the tradeoff you made and when you'd add it back in."*
* Instructor note: Looks for reasoning about scope discipline and a specific future trigger for expanding scope — not just "reviews are unstructured."

**Gate 3 (end of Module 03):** *"Why is testing a Genie Agent with benchmarks and an API smoke test a stronger claim to a customer than testing it live in the chat UI during a demo?"*
* Instructor note: Looks for the repeatability, inspectability, and documentation argument — not just "it's more thorough."

**Gate 4 (end of Module 04):** *"A customer asks why they should trust an MCP-exposed Genie Agent inside Slack as much as they trust the chat UI directly. What do you tell them?"*
* Instructor note: Correct answer references that governance is identical because it is inherited from Unity Catalog regardless of the surface — same underlying grants, not a re-explanation of what MCP is.

**Gate 5 (end of Module 05):** *"Why is it important that the business user can run the same Genie Agent without inheriting the analyst's or admin's data access? Explain what part of the system is enforcing that separation."*
* Instructor note: Correct answer distinguishes between agent sharing permissions and Unity Catalog data permissions, and explains that data access is evaluated as the current user even when the warehouse credentials are embedded by the agent author.

## Anti-automation policy for this lab

A participant should **not** be marked complete solely because the technical assets exist. The participant must still demonstrate comprehension.

If a participant's answers are unusually fast, overly polished, generic, or disconnected from the specific lab artifacts, require one more proof step:

* ask them to explain their Module 05 governance proof out loud, or
* ask them to explain one benchmark mismatch using their own run results, or
* ask them to justify why `RAD_PRODUCT_REVIEWS` was excluded initially and when they would bring it back

If they cannot do that without outside help, they have not yet passed the gate.

---



---

[← Cleanup](06-cleanup.md)  |  [🏠 Home](README.md)  |  [Instructor Guide →](08-instructor-guide.md)
