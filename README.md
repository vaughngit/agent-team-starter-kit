# Agent Team Starter Kit

A practical starter kit for turning vague AI-agent work into reviewable engineering work.

This repo is for people experimenting with autonomous coding agents, agent boards, Paperclip-style workflows, Codex/Claude-style coding agents, or local assistants like Hermes. The goal is not to make agents "just ship." The goal is to make agents return with receipts: acceptance criteria, validation gates, screenshots, logs, review artifacts, and a clear human decision point.

## What problem this solves

Autonomous agents are getting better at taking an issue and returning a branch or pull request.

The harder problem is the verification loop:

- Did the agent understand the issue?
- Did it work in the right repo/workspace?
- Did it keep scope tight?
- Did it test the right behavior?
- Did it use safe fixtures instead of risky real data?
- Did it produce evidence a human can inspect without replaying the entire task?

This starter kit gives you baseline templates for that loop.

## What is included

- `templates/mini-prd-template.md` — a lightweight intent/spec template for turning rough product ideas into agent-ready work.
- `templates/agent-issue-template.md` — a generic issue / execution contract template for agent work.
- `templates/ui-evidence-validation-template.md` — a stricter validation template for UI changes that need screenshots or browser automation.
- `templates/completion-evidence-template.md` — a checklist for what an agent should return before work is considered reviewable.
- `templates/changelog-entry-template.md` — a durable narrative layer for multi-issue initiatives and downstream activation decisions.
- `templates/adoption/` — turnkey adoption snippets for project `AGENTS.md`, Claude, Copilot, Cursor, Paperclip, Linear, and adoption-checklist workflows.
- `templates/advanced/context-equivalence-checklist.md` — an optional advanced checklist for migrations or workflow replacements where hidden context must be preserved.
- `examples/example-agent-issue.md` — an example issue using fake/synthetic context.
- `examples/example-changelog-entry.md` — an example changelog entry using fake/synthetic context.
- `examples/example-adopted-project-instructions.md` — an example of project-local instructions after adoption.
- `docs/agent-adoption-protocol.md` — agent-facing protocol for installing this framework into a target project without manual copy/paste.
- `docs/agent-team-operating-model.md` — a lightweight operating model for human, supervisor, coding agent, reviewer, and repo roles.
- `docs/source-of-truth-model.md` — guidance for telling agents which system owns intent, status, code, docs, secrets, and approval.
- `docs/changelog-workflow.md` — guidance for when and how agent teams should update a changelog.
- `docs/when-to-use-a-full-prd.md` — guidance for when a mini-PRD is enough and when to promote to a full PRD.

## Core idea: agent teams need handoffs

Treat each agent task as an execution contract:

1. A human gives the vision, often as a mini-PRD, user story, bug report, or rough product note.
2. A local assistant or supervisor agent turns that vision into a structured issue.
3. The issue defines context, acceptance criteria, safety boundaries, validation gates, and required evidence.
4. A focused implementation agent works in its own context window.
5. A branch / PR / MR becomes the handoff unit.
6. A reviewer or QA agent checks the work against the issue contract.
7. The implementation agent returns evidence: commands, screenshots, logs, test output, review notes.
8. A human reviews receipts, not claims.

This is the main point: the template is not just a form. It is the shared contract between the human, the planning agent, the implementation agent, the reviewer agent, and the code review surface.

## How the templates get used

Templates only help if something in your workflow explicitly points to them.

There are two adoption paths:

1. **Turnkey AI-agent adoption** — point an AI assistant or coding agent at this repo and the target project, then tell it to follow `docs/agent-adoption-protocol.md`.
2. **Manual adoption** — copy the relevant templates or snippets into your project instructions, issue tracker, or runbook.

The preferred path is turnkey adoption. The starter kit includes an agent-facing protocol and adoption snippets so the human does not have to manually translate the framework.

Use a prompt like:

```text
Adopt the Agent Team Starter Kit into this project.

Read https://github.com/vaughngit/agent-team-starter-kit and follow docs/agent-adoption-protocol.md.
Inspect this target repo, then create or update the local agent instructions, issue-template guidance, validation gates, completion-evidence guidance, source-of-truth split, and changelog/docs impact rules.
Return a patch or PR with changed files, validation performed, assumptions, and remaining human decisions.
```

If you are adopting manually, start with `templates/adoption/AGENTS.md-snippet.md` and then add the tracker-specific or tool-specific files that match your project.

## Use with Paperclip or other agent systems

These templates are intentionally tool-agnostic. They work best with systems that can track issues, agents, branches, comments, and review artifacts.

Paperclip, GitHub Issues, Linear, Jira, or any other tracker only benefit from this kit when the issue body, project docs, or local agent instructions point agents at the execution-contract pattern.

A practical Paperclip flow:

1. Human gives Hermes a mini-PRD, user story, bug, or rough vision.
2. Hermes reads the template and creates a Paperclip issue shaped as an execution contract.
3. Hermes uses board-authorized authentication to post the issue to Paperclip as a board user/operator.
4. Paperclip stores that issue in `backlog` until activation.
5. When the issue is assigned and moved to `todo`, a focused Paperclip agent receives the issue context.
6. The agent implements, validates, and posts completion evidence back to the issue and PR/MR.
7. The human or reviewer checks the evidence against the issue contract before accepting the work.
8. For dependent or multi-issue work, the changelog records what changed, what evidence was reviewed, and whether downstream work is ready to activate.
9. A reviewer or human decides whether the receipts are good enough to accept the work.

If you are using GitHub Issues, Linear, Jira, or another tracker, use the same sections there and link the resulting branch or pull request.

## Safety note

Do not put secrets, production credentials, private customer data, financial data, tokens, or private repository links in public issues or public examples. Use secret-manager pointers and safe fixtures instead.

## License

MIT
