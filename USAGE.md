# How to use agent-spec-kit

There are two ways to run the spec interview, depending on which Claude environment you use.

## Option 1: Standalone (Claude.ai or Claude desktop/mobile)

This is the simplest option and requires no installation.

1. Open a new conversation in Claude.ai (or the Claude desktop or mobile app).
2. Open `prompt.md` from this repository.
3. Copy the entire contents of `prompt.md`.
4. Paste it as your first message in the new conversation.
5. Send it. The interviewer will introduce itself and start asking questions.
6. At the end of the interview, the spec will be produced inline in the conversation. Copy it out and save it wherever you want.

The interview takes 15-30 minutes for the long version and 10-15 minutes for the short version.

## Option 2: Claude Code subagent

If you use Claude Code as your AI coding assistant, the interview is available as a subagent that writes the spec directly to a file.

1. Clone this repository (or copy the `.claude/agents/spec-interviewer.md` file into your own project's `.claude/agents/` directory).
2. From the project root, launch Claude Code: `claude`.
3. Invoke the subagent: `/spec-interview` (or describe what you want, and Claude Code will route to the spec-interviewer subagent automatically).
4. The interviewer will start asking questions. Answer them in the terminal.
5. At the end of the interview, the spec will be written to `agent-spec.md` in your project root (or to a path you specify during the interview).

## What you'll get

A complete agent specification document with three layers:

- **Constitution** (4 sections): inviolable constraints, required behaviors, regulatory scope, escalation triggers
- **Specification** (7 sections): primary job, users, in-scope and out-of-scope behaviors, inputs, outputs, success criteria
- **Operating envelope** (6 sections): autonomy boundaries, tool contracts, termination logic, failure handling, plus optional confidence and audit sections

Sections you skipped during the interview will be flagged with `[NEEDS REVIEW]` markers so you can come back to them later.

For a worked example of what a complete spec looks like, see the files in the `examples/` directory.

## Tips for a good interview

- **Have a clear primary job in mind before you start.** The interview will go faster if you can articulate what the agent does in one sentence.
- **Don't worry about being perfect.** Use "I don't know" or "skip for now" freely. The spec will flag those sections, and you can come back to them.
- **Push yourself on the constitutional layer.** This is the section most agents get wrong. The interviewer will probe; let it probe.
- **Treat this as a v1.** No spec is final after one pass. Plan to iterate after the interview, especially on the operating envelope sections.
