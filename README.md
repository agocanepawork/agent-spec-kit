# agent-spec-kit

**Spec-driven development for AI agents. The agent-shaped extension of the idea behind GitHub's Spec Kit.**

Most spec-driven development tooling assumes the artifact you're producing is code. That assumption breaks when the artifact is an agent. Agents have autonomy boundaries, tool contracts, termination logic, and failure modes that don't exist in code-generation workflows.

`agent-spec-kit` is a small toolkit for specifying AI agents using a structured interview, producing a markdown specification document organized in three layers: **constitution**, **specification**, and **operating envelope**.

**Not affiliated with GitHub's \\\[Spec Kit](https://github.com/github/spec-kit** The naming references Spec Kit deliberately because the two projects compose. agent-spec-kit produces the agent contract; Spec Kit produces the implementation. See \\\[Using with Spec Kit](#using-with-spec-kit) below.

\---

## Why this exists

The argument for this project is laid out in two LinkedIn articles:

* **Specs Are Eating PRDs** — *https://www.linkedin.com/pulse/specs-eating-prds-agostino-canepa-2q9me*
The broader trend: SDD is reshaping product management as code generation gets cheaper. PMs who can write precise behavioral specs will matter more than PMs who can paint a vision.
* **Why Specifying an Agent Is Harder Than Specifying Code** — *\[paste article URL after publishing]*
The specific problem this toolkit addresses: five things break when you move from specifying code to specifying agents. The current SDD tooling models none of them.

The project also draws on academic work in the area:

* Marri, S.R. (2026). *Constitutional Spec-Driven Development: Enforcing Security by Construction in AI-Assisted Code Generation*. arXiv:2602.02584. The constitutional layer in this toolkit is directly inspired by Marri's framing.
* Piskala, D.B. (2026). *Spec-Driven Development: From Code to Contract in the Age of AI Coding Assistants*. arXiv:2602.00180. The three-level rigor model (spec-first, spec-anchored, spec-as-source) underlies the methodology choices.

\---

## What this is

A small repository containing:

* A canonical interview prompt that conducts the spec interview (`prompt.md`).
* A spec template that defines the output structure with section-by-section guidance (`templates/agent-spec-template.md`).
* A Claude Code subagent definition for users of Claude Code (`.claude/agents/spec-interviewer.md`).
* Two example specifications produced by running the tool (`examples/`).

Time to produce a complete agent spec from scratch: 15-30 minutes.

\---

## Using with Spec Kit

`agent-spec-kit` and GitHub's [Spec Kit](https://github.com/github/spec-kit) operate at different stages of the agent-building lifecycle and produce different artifacts. They compose. They don't compete.

**`agent-spec-kit` produces the *what*.** The agent contract: what the agent must never do, what it does, what tools it can call, how it knows it's done, what its failure modes are. Output: a behavioral specification.

**Spec Kit produces the *how*.** The implementation: API design, data models, deployment architecture, observability infrastructure, the code that wires up tool calls and runs the agent. Output: working software.

The two artifacts serve different audiences. `agent-spec-kit`'s output is consumed by anyone who needs to reason about the agent's contract: compliance reviewers, operations teams, and the implementation team that needs to build within the boundaries the contract sets. Spec Kit's output is consumed by the implementation toolchain: developers, code-generation systems, deployment infrastructure. The two are not substitutes. Hand Spec Kit's output to a compliance reviewer and they will ask where the constitutional constraints are. Hand `agent-spec-kit`'s output to a code-generation pipeline and it will ask where the implementation tasks went.

### The combined workflow

1. **Run `agent-spec-kit` first.** Conduct the interview, produce `agent-spec.md`. This becomes the contract that governs everything downstream.
2. **Use the agent-spec as input to Spec Kit's `/specify` phase.** Spec Kit's feature spec now operates within the agent contract rather than starting from a blank page. The constitution, autonomy boundaries, and tool contracts are inputs, not questions Spec Kit has to ask.
3. **Continue through Spec Kit's normal workflow.** `/plan` produces the implementation architecture. `/tasks` breaks it into work. `/implement` generates the code.
4. **The agent contract continues to govern.** When implementation questions come up ("should the agent escalate this case or handle it autonomously?"), the answer is in the agent-spec. The agent-spec is not disposable after implementation begins; it is the reference document the implementation must respect.

### Using either tool independently

You can use either tool on its own. `agent-spec-kit` produces a complete agent specification you can hand to any team or any downstream toolchain. Spec Kit produces working software for any feature, agent or not. The composition is most useful for teams building agents in regulated or high-stakes contexts where the agent contract has real load-bearing weight.

### Do you actually need `agent-spec-kit`?

Honest answer: not strictly. You could prompt Spec Kit's `/specify` phase to cover agent-specific content (autonomy, tools, termination, constitution) and get a passable result. `agent-spec-kit` gives you a pre-built, opinionated structure for that content, enforced consistency across multiple agent specs, and constitution-first ordering as an architectural priority. These are real but modest benefits, more about discipline than capability. The value is highest when you are specifying multiple agents on the same team and want a consistent shape across them, or when the agent operates in a domain where the constitutional layer carries real weight.

\---

## Quick start

### Standalone (Claude.ai or Claude desktop/mobile)

1. Open a new conversation in Claude.
2. Copy the contents of [`prompt.md`](prompt.md).
3. Paste as your first message and send.
4. Answer the questions. The spec is produced inline at the end.

### Claude Code

1. Clone this repository (or copy `.claude/agents/spec-interviewer.md` into your own project).
2. From the project root, run `claude`.
3. Invoke the subagent: describe what you want, or use the slash command if available in your version.
4. Answer the questions. The spec is written to `agent-spec.md` in the project root.

See [USAGE.md](USAGE.md) for detailed instructions and tips.

\---

## What the spec looks like

A complete spec produced by this tool has three layers and 17 sections.

### Layer 1: Constitution

*The non-negotiable constraints that govern everything else in the spec.*

1. Inviolable constraints
2. Required behaviors
3. Regulatory and compliance scope
4. Escalation triggers

### Layer 2: Specification

*What the agent does, who it serves, what its observable behaviors look like.*

5. Primary job
6. Users and roles
7. In-scope behaviors
8. Out-of-scope behaviors
9. Inputs and triggers
10. Outputs and artifacts
11. Success criteria

### Layer 3: Operating envelope

*The agent-specific layers that distinguish this from a code spec.*

12. Autonomy boundaries
13. Tool contracts
14. Termination logic
15. Failure handling
16. Confidence and uncertainty *(long version only)*
17. Audit and observability *(long version only)*

The constitution-first ordering is deliberate. In regulated and high-stakes contexts, the constitution is the foundation everything else fits inside. The downstream sections must respect it, not the other way around.

For the full template with detailed guidance per section, see [`templates/agent-spec-template.md`](templates/agent-spec-template.md).

\---

## Examples

The [`examples/`](examples/) directory contains two complete specs produced by running the tool:

* [**`01-agent-spec-kit-itself.md`**](examples/01-agent-spec-kit-itself.md) — the tool specifies itself. Demonstrates the simple-agent case. Useful as a worked example of what a complete output looks like, and as a meta proof point that the tool is good enough to spec itself.
* [**`02-clinical-literature-agent.md`**](examples/02-clinical-literature-agent.md) — specification for a clinical literature research agent. Demonstrates the regulated-domain case where the constitutional layer, autonomy boundaries, tool contracts, and confident-wrong-action failure modes do real work. Buildable on public APIs (PubMed, ClinicalTrials.gov, FDA feeds).

Both examples were produced using the long version of the interview.

\---

## Project structure

```
agent-spec-kit/
├── README.md                              ← this file
├── LICENSE                                ← MIT
├── USAGE.md                               ← detailed usage instructions
├── prompt.md                              ← canonical interview prompt
├── templates/
│   └── agent-spec-template.md             ← spec output structure with guidance
├── examples/
│   ├── 01-agent-spec-kit-itself.md        ← the tool specs itself
│   └── 02-clinical-literature-agent.md    ← regulated-domain example
└── .claude/
    └── agents/
        └── spec-interviewer.md            ← Claude Code subagent definition
```

\---

## Design choices worth knowing

A few decisions baked into this project that you may want to understand before adopting it.

**Constitution comes first.** The template puts inviolable constraints, required behaviors, regulatory scope, and escalation triggers at the top. Most SDD tooling treats these as optional or relegates them to an appendix. For regulated domains and high-stakes agents, this ordering is load-bearing.

**Out-of-scope is a first-class section.** The template gives "out-of-scope behaviors" the same weight as "in-scope behaviors." Agent failures often come from doing things the spec didn't say to avoid. Naming them explicitly closes that gap.

**Tool specs are first-class artifacts.** Section 13 (Tool contracts) requires every tool the agent can call to be specified at a behavioral level: purpose, inputs, outputs, failure modes. This is a layer of specification work that does not exist in code-generation SDD.

**Termination logic is explicit, not implicit.** Section 14 forces the spec to define how the agent knows it is done. Without this, agents either loop forever or stop too early with confident-looking partial results.

**Failure modes are framed as confident wrong actions.** Section 15 specifically asks for the failure modes where the agent thinks it succeeded but didn't, not just where the agent crashes. This is the dangerous failure mode in production, and the one most code-style specs miss.

**The interviewer pushes back on dangerous answers.** The prompt includes specific patterns for challenging users who say "no constitutional constraints," "fully autonomous," or "the LLM will figure it out." The spec produced is only as good as the answers given; the interviewer has a responsibility to probe.

\---

## What this is not

* **Not a replacement for Spec Kit.** Not a fork, not a successor, not affiliated. Designed to compose with Spec Kit (see [Using with Spec Kit](#using-with-spec-kit)), not to replace it. They operate at different stages of the agent-building lifecycle.
* **Not an agent runtime.** This produces specifications. It does not run agents, orchestrate agents, or coordinate multi-agent systems.
* **Not a regulatory compliance tool.** This is a methodology for capturing constraints. Verifying compliance against any specific framework remains the user's responsibility.
* **Not a substitute for human review.** Generated specs should be reviewed by humans with relevant domain knowledge before use. The interview surfaces the right questions; humans answer them and verify the answers are right.

\---

## Background

I spent the last year writing specifications for clinical AI agents in a regulated domain. Most of the SDD playbook either didn't apply or had to be extended in ways the current tooling doesn't model. `agent-spec-kit` is the toolkit I wished I'd had at the beginning of that work.

For the longer version of this argument, see the LinkedIn articles linked in [Why this exists](#why-this-exists).

\---

## Acknowledgments

* **GitHub's** [**Spec Kit**](https://github.com/github/spec-kit) for establishing the SDD workflow vocabulary that this project builds on, and for proving that structured AI workflows produce better outputs than loosely-prompted ones.
* **Srinivas Rao Marri** for the constitutional framing in *Constitutional Spec-Driven Development*.
* **Deepak Babu Piskala** for the spec-rigor spectrum in *Spec-Driven Development: From Code to Contract*.
* **Anthropic** for Claude and Claude Code.

\---

## License

MIT. See [LICENSE](LICENSE).

\---

## Contact

Agostino Canepa
[LinkedIn](https://www.linkedin.com/in/agocanepa/)

