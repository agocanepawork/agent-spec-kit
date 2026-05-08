# Agent Specification: [Agent Name]

> **A note on how to use this template.** A complete agent spec leaves no gaps for an implementer to guess at. If a question is left ambiguous in this document, the implementing agent or developer will resolve it incorrectly. The point of the template is not to produce a long document. The point is to produce a document where every section is answered with enough precision that two different readers reach the same understanding of what the agent should do.
>
> Sections marked **(required)** must be filled in for the spec to be considered complete. Sections marked **(conditional)** apply only if relevant to the agent. Sections marked **(recommended)** improve spec quality but are not strictly necessary for simple agents.

---

## Table of Contents

**Layer 1: Constitution**
1. Inviolable constraints
2. Required behaviors
3. Regulatory and compliance scope
4. Escalation triggers

**Layer 2: Specification**
5. Primary job
6. Users and roles
7. In-scope behaviors
8. Out-of-scope behaviors
9. Inputs and triggers
10. Outputs and artifacts
11. Success criteria

**Layer 3: Operating envelope**
12. Autonomy boundaries
13. Tool contracts
14. Termination logic
15. Failure handling
16. Confidence and uncertainty
17. Audit and observability

---

# Layer 1: Constitution

The constitution defines what this agent must never do and must always do, regardless of anything stated elsewhere in the spec. If any other section appears to conflict with the constitution, the constitution wins.

## 1. Inviolable constraints (required)

*The things this agent must never do, no matter what the user asks, no matter what the situation appears to demand.*

Capture the absolute prohibitions. These are the constraints that must hold even under adversarial prompting, ambiguous input, or unusual tool combinations. Each constraint should be specific enough that a reviewer can tell whether the agent violated it.

Example bullets:
- The agent must never produce content that constitutes medical advice or treatment recommendations.
- The agent must never disclose patient-identifying information outside the authenticated session.
- The agent must never modify a record in a system of truth without explicit user confirmation.

## 2. Required behaviors (required)

*The things this agent must always do, regardless of efficiency, brevity, or user pressure.*

Capture the non-negotiable positive behaviors. These are often the inverse of failure modes you want to prevent.

Example bullets:
- The agent must always cite the source for any factual claim it produces.
- The agent must always log the tools it called and the parameters it passed.
- The agent must always present its confidence level when surfacing a recommendation.

## 3. Regulatory and compliance scope (conditional)

*The regulatory frameworks this agent operates under, if any.*

If the agent operates in a regulated domain, list the applicable frameworks (HIPAA, GDPR, PCI-DSS, SOC 2, FedRAMP, FDA guidance, etc.) and note which constitutional constraints derive from them. If the agent operates outside any regulatory framework, state that explicitly. Do not leave this section blank.

Example: *This agent operates under HIPAA. All constitutional constraints related to PHI handling derive from the HIPAA Privacy Rule.*

## 4. Escalation triggers (required)

*The situations where the agent must hand off to a human, regardless of what the rest of the spec implies the agent could do.*

Capture the conditions under which autonomous action stops being appropriate. These are not failure modes; they are situations where the correct behavior is "stop and ask for human judgment."

Example bullets:
- Any input that suggests the user may be in crisis or in danger.
- Any tool call that would result in irreversible action above a defined threshold.
- Any situation where the agent's confidence in its planned action falls below a defined level.

---

# Layer 2: Specification

The specification defines what this agent does, who it serves, and what its observable behaviors look like. This is the layer most analogous to a traditional product spec.

## 5. Primary job (required)

*The single, focused job this agent exists to do, in one paragraph.*

If you cannot describe the agent's primary job in one paragraph, the job is not focused enough. An agent with two primary jobs is two agents. The primary job statement is the test against which every other section is measured: anything that does not serve the primary job is either out of scope or belongs in a different agent.

Example: *This agent helps physicians stay current on clinical evidence in their practice area. It monitors specified medical literature sources for new publications, summarizes findings in a format the physician can review in under two minutes per item, and flags items that may warrant a change in practice.*

## 6. Users and roles (required)

*Who interacts with this agent, in what capacity, with what level of trust.*

List each user role distinctly. Different roles often have different permissions, different escalation paths, and different definitions of success. A spec that conflates roles will produce an agent that treats users incorrectly.

Example bullets:
- **Primary user (physician).** Authenticated clinical user. Authorized to receive summaries, mark items as reviewed, and configure source preferences.
- **Administrator.** Authenticated administrative user. Authorized to manage source lists and user accounts. Not authorized to view individual users' reading history.

## 7. In-scope behaviors (required)

*What the agent is supposed to do.*

List the behaviors that fall within the agent's primary job. Each behavior should be specific enough to be testable. "Helps users with X" is not a behavior. "Produces a summary of X with sections A, B, C" is.

Example bullets:
- Retrieves new publications from configured sources on a defined schedule.
- Produces a structured summary of each new publication with these fields: title, source, publication date, key findings, methodology summary, relevance score.
- Surfaces items above a configured relevance threshold to the user's review queue.

## 8. Out-of-scope behaviors (required)

*What the agent is explicitly not supposed to do, even if it could.*

This section is as important as in-scope. An agent that knows what it should not do is dramatically more reliable than one that only knows what it should do. List the behaviors that are technically possible but deliberately excluded.

Example bullets:
- The agent does not make treatment recommendations, even when the underlying literature contains them.
- The agent does not communicate with patients directly.
- The agent does not modify the source publications or store full-text copies; it works from metadata and abstracts only.

## 9. Inputs and triggers (required)

*How the agent gets activated, what context it receives, what data it operates on.*

Specify each entry point. Agents typically have multiple triggers (user-initiated, scheduled, event-driven), and each carries different context. Be explicit about what data is available at each trigger.

Example bullets:
- **Scheduled trigger:** Runs daily at user-configured time. Receives the user's source preferences and last-run timestamp.
- **User-initiated trigger:** "Check for updates now" command. Receives the same context as scheduled trigger plus a user session token.

## 10. Outputs and artifacts (required)

*What the agent produces, in what form, with what structure.*

Specify the format and structure of every output. If the output is structured data, specify the schema. If the output is text, specify the format and any required sections. Outputs that are inconsistently structured will be misinterpreted by downstream consumers.

Example: *Each summary item is a JSON object with fields: id, title, source, publication_date, key_findings (array of strings), methodology_summary (string), relevance_score (0-100), citations (array of objects). Items are written to the user's review queue and not returned inline.*

## 11. Success criteria (required)

*How we know this agent is working correctly. Include numerical metrics where they apply, but do not force-fit metrics where they do not.*

Capture both quantitative measures (where appropriate) and observable qualitative behaviors. For agents, success is often as much about what the agent does not do (does not invent findings, does not exceed scope) as what it does.

Example bullets:
- 95% of new publications matching configured sources and date ranges are retrieved within 24 hours.
- Summaries pass random clinical review for accuracy at a rate of 99% or better.
- The agent never produces a summary citing a source that does not exist.
- Users mark surfaced items as relevant at a rate of 70% or better (calibration of relevance threshold).

---

# Layer 3: Operating envelope

The operating envelope defines the agent-specific layers that distinguish an agent spec from a code spec: how the agent decides, how it uses tools, how it knows it is done, how it handles failure, and how it can be observed. These sections do not have direct analogs in traditional software specifications.

## 12. Autonomy boundaries (required)

*What the agent can decide on its own, what requires human approval, and how the boundary is enforced.*

For each meaningful decision the agent can make, specify whether the agent acts autonomously, surfaces for human approval, or branches based on a condition. Unstated autonomy is the most common source of agent failures: an agent that takes an action you did not realize it could take is operating outside the spec.

Example bullets:
- **Autonomous:** Selecting which sources to query, ordering retrieved items, computing relevance scores.
- **Conditional autonomy:** Marking items as auto-archived if relevance score is below 20; below 50 requires user review.
- **Human approval required:** Adding a new source to the user's configured list; modifying relevance scoring thresholds.

## 13. Tool contracts (required)

*The tools this agent has access to, what each is for, when to use which, and what failure modes each can produce.*

Every tool the agent can call is itself a small specification. Describe each tool's purpose, inputs, outputs, and failure modes. Specify the conditions under which the agent should choose one tool over another when multiple could apply.

Example bullets:
- **`search_pubmed(query, date_range, max_results)`.** Queries PubMed for matching publications. Returns metadata records, never full text. Failure modes: rate limiting (handled with backoff), no results (returns empty list, not error), malformed query (returns error, agent should reformulate). Use this tool for primary literature searches.
- **`fetch_clinicaltrials(query, status_filter)`.** Queries ClinicalTrials.gov. Use this tool when the user's source preferences include clinical trial registries. Do not use as a substitute for `search_pubmed`.

## 14. Termination logic (required)

*How the agent decides it is done. How it handles the case where it cannot tell.*

Multi-step agents must explicitly know when to stop. An agent without termination logic either loops indefinitely or stops too early with a confident-looking partial result. Specify the conditions under which the agent considers its task complete, and the conditions under which it must explicitly report incomplete or uncertain status.

Example: *The agent considers a daily run complete when: (a) all configured sources have been queried at least once, (b) all retrieved items have been scored and either surfaced or archived, and (c) the run summary has been written to the user's queue. If any source query fails after retry, the agent must explicitly report a partial run with the failed source identified, rather than treating the run as complete.*

## 15. Failure handling (required)

*The specific failure modes this agent has, and how the spec defends against each.*

The most dangerous agent failure mode is the confident wrong action: the agent interprets a situation incorrectly, picks a plausible-but-wrong action, executes successfully, and reports completion. Identify the specific ways this agent can produce confident wrong actions, and specify how the agent should detect or prevent each.

Example bullets:
- **Failure mode: summarizing a study incorrectly.** Defense: the agent must always include direct quotations from the abstract for any quantitative claim, so reviewers can verify against source.
- **Failure mode: missing a relevant publication.** Defense: the agent reports the count of items retrieved per source per run; sustained zero-result returns from a source trigger an alert.
- **Failure mode: scope creep into recommendations.** Defense: the agent's output schema forbids any field for "recommendation"; reviews of output schema are part of the QA cycle.

## 16. Confidence and uncertainty (recommended)

*How the agent expresses certainty in its outputs, and when it should defer rather than answer.*

For agents that produce judgments (relevance scores, classifications, summaries), specify how the agent communicates its confidence and what it does when confidence is low. An agent that always speaks with the same level of certainty regardless of underlying evidence quality will mislead its users.

Example: *Relevance scores below 30 are surfaced with a "low confidence" flag and never trigger automated archiving. Summaries derived from abstracts only (no full text available) are flagged as "abstract-based" so the user knows the depth of analysis is limited.*

## 17. Audit and observability (recommended)

*What the agent logs, and how its decisions can be reviewed after the fact.*

Specify what the agent records about its own behavior: tool calls made, decisions taken, items processed, items skipped and why. Without observability, debugging agent failures requires re-running the agent under the same conditions, which is often impossible. Without audit logs, regulated agents cannot pass compliance review.

Example bullets:
- The agent logs every tool call with parameters, response status, and timestamp.
- The agent logs every relevance scoring decision with the inputs and computed score.
- The agent produces a per-run summary log containing: sources queried, items retrieved, items surfaced, items archived, errors encountered.

---

*This spec was produced using [agent-spec-kit](https://github.com/agocanepawork/agent-spec-kit), an SDD framework for specifying AI agents.*
