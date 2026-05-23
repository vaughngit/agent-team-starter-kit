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

- `templates/agent-issue-template.md` — a generic issue / execution contract template for agent work.
- `templates/ui-evidence-validation-template.md` — a stricter validation template for UI changes that need screenshots or browser automation.
- `templates/completion-evidence-template.md` — a checklist for what an agent should return before work is considered reviewable.
- `examples/example-agent-issue.md` — an example issue using fake/synthetic context.
- `docs/agent-team-operating-model.md` — a lightweight operating model for human, supervisor, coding agent, reviewer, and repo roles.

## Core idea: agent teams need handoffs

Treat each agent task as an execution contract:

1. A human gives the vision, often as a short PRD, user story, bug report, or rough product note.
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

A common pattern:

1. Put these templates in a repo, knowledge base, or team docs folder.
2. Tell your local assistant, supervisor agent, or project `AGENTS.md` to read the template before creating issues.
3. When the assistant creates an issue, it copies the relevant sections into the issue body.
4. The agent board or tracker stores that issue as the execution contract.
5. Implementation and reviewer agents receive the issue body as task context.
6. Completion evidence is posted back to the same issue or PR/MR.

For example, if Hermes is acting as the local assistant, you can say:

```text
Use templates/agent-issue-template.md as the baseline.
Turn this PRD/user story into a structured agent issue.
Include acceptance criteria, safety boundaries, validation gates, and completion evidence fields.
Keep it in backlog unless I explicitly say to activate an agent.
```

If you want this to happen automatically in a project, add an instruction to that project's `AGENTS.md`, `.github/copilot-instructions.md`, or team runbook:

```text
Before creating agent issues, read templates/agent-issue-template.md.
For UI changes, also read templates/ui-evidence-validation-template.md.
For completion comments, use templates/completion-evidence-template.md.
```

## Use with Paperclip or other agent systems

These templates are intentionally tool-agnostic. They work best with systems that can track issues, agents, branches, comments, and review artifacts.

Paperclip does not magically know about this repo just because it exists. The template has to be included in the issue body, linked in the project docs, or referenced in the project/agent instructions that Paperclip agents read.

A practical Paperclip flow:

1. Human gives Hermes a PRD, user story, bug, or rough vision.
2. Hermes reads the template and creates a Paperclip issue shaped as an execution contract.
3. Hermes uses board-authorized authentication to post the issue to Paperclip as a board user/operator.
4. Paperclip stores that issue in `backlog` until activation.
5. When the issue is assigned and moved to `todo`, a focused Paperclip agent receives the issue context.
6. The agent implements, validates, and posts completion evidence back to the issue and PR/MR.
7. A reviewer or human decides whether the receipts are good enough to accept the work.

If you are using GitHub Issues, Linear, Jira, or another tracker, use the same sections there and link the resulting branch or pull request.

## Safety note

Do not put secrets, production credentials, private customer data, financial data, tokens, or private repository links in public issues or public examples. Use secret-manager pointers and safe fixtures instead.

## License

MIT
