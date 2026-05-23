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

## Core idea

Treat each agent task as an execution contract:

1. Human gives the vision.
2. The vision becomes a structured issue.
3. The issue defines acceptance criteria, safety boundaries, and validation gates.
4. A focused agent works in a focused context window.
5. A branch / PR / MR becomes the handoff unit.
6. The agent returns evidence: commands, screenshots, logs, test output, review notes.
7. A human reviews receipts, not claims.

## Use with Paperclip or other agent systems

These templates are intentionally tool-agnostic. They work best with systems that can track issues, agents, branches, comments, and review artifacts.

If you are using Paperclip, paste the issue template into a Paperclip issue and adapt the Routing and Validation sections for your project.

If you are using GitHub Issues, Linear, Jira, or another tracker, use the same sections there and link the resulting branch or pull request.

## Safety note

Do not put secrets, production credentials, private customer data, financial data, tokens, or private repository links in public issues or public examples. Use secret-manager pointers and safe fixtures instead.

## License

MIT
