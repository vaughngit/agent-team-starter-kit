# Source-of-Truth Model for Agent Teams

Agent teams move faster when each system has a clear job. Without that split, agents can treat stale comments, old docs, private prompts, or incomplete issue text as authoritative.

Use this model to tell agents where to look and what to trust.

## Default split

| Layer | What it is authoritative for | What it is not for |
|---|---|---|
| Human owner | Intent, risk tolerance, final acceptance, approval for sensitive actions | Storing every implementation detail |
| Issue tracker / agent board | Live task state, assignments, comments, blockers, completion evidence | Long-term architecture rationale by itself |
| Repo / code review surface | Code, diffs, commits, tests, CI, PR/MR review artifacts | Product intent unless linked from docs/issues |
| Project docs / knowledge base | Requirements, architecture, operating rules, decisions, read order | Secret values or live issue status |
| Changelog / decision log | What changed, why it matters, evidence reviewed, downstream activation decisions | Full raw logs or every agent thought |
| Secret manager | Secret values and credential metadata | Public instructions, issue text, or logs |
| Local assistant / supervisor | Turning human intent into structured issues and checking references | Quietly approving production-risk actions |
| Implementation agent | Focused execution from the issue contract | Redefining scope or approval boundaries |
| Reviewer / QA agent | Independent evidence review and gap detection | Final business approval unless delegated |

## What to put in an agent issue

A good agent issue should point to the right sources instead of copying everything:

- Repo/workspace and base branch/ref
- Project README / `AGENTS.md` / team runbook
- Relevant product/design notes
- Relevant changelog or decision-log entry
- Credential pointer names, without secret values
- Validation commands and evidence expectations
- Human approval boundaries

## What to install during project adoption

When an agent adopts this starter kit into a project, it should add project-local guidance that names the actual files or systems for each layer:

- Local agent instruction file: usually `AGENTS.md`, `CLAUDE.md`, `.github/copilot-instructions.md`, or equivalent
- Issue template or tracker guidance: where agent execution contracts are created
- Completion-evidence template: where agents post receipts
- Changelog or decision-log convention: `CHANGELOG.md`, `docs/decisions/`, `docs/change-management/`, or project-specific equivalent
- Knowledge-base boundary: what belongs outside the repo, if the project uses an external knowledge base
- Secret-manager boundary: pointer names only, never secret values

Use `docs/agent-adoption-protocol.md` for the turnkey adoption flow.

## What not to put in an agent issue

Avoid putting these into public or broadly visible issue text:

- Secret values, tokens, passwords, cookies, or private keys
- Customer/private production data
- Private repository links in public examples
- Large raw logs when a summary and artifact link would do
- Ambiguous statements such as "use the usual process" without a link

## Read-order pattern

If there are multiple docs, define a read order:

```text
1. Read the issue body for task scope and acceptance criteria.
2. Read the project README / AGENTS.md for operating rules.
3. Read the linked product/design note for intent.
4. Read the changelog or decision log for previous evidence and downstream dependencies.
5. Treat older docs as historical unless the read order says they are current.
```

## Authority checks for agents

Before acting, an agent should be able to answer:

- Which issue or task am I executing?
- Which repo/workspace/ref am I allowed to change?
- Which docs define current behavior?
- What evidence must I produce?
- What data or actions are out of bounds?
- Who accepts the result?

If any answer is missing, the agent should ask for clarification or create a blocker instead of guessing.

## Common failure modes

- Treating a stale doc as current because no read order exists.
- Treating implementation completion as human acceptance.
- Posting secret values into issue comments.
- Expanding scope because the agent found a related problem.
- Marking work done without reviewable evidence.
- Losing the rationale for multi-issue work because it only lives in chat or agent logs.
