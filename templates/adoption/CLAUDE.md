# Claude Code project instructions

Adopt the Agent Team Starter Kit pattern in this project.

Before implementing work:

1. Read the project issue or task for scope and acceptance criteria.
2. Read local project instructions, especially `AGENTS.md`, `CLAUDE.md`, README, and linked runbooks.
3. Confirm the source-of-truth split for issues, repo docs, changelog, knowledge base, secret manager, and human approval.
4. If the task is vague, shape it into an execution contract before coding.

When implementing:

- Keep the change to one logical scope.
- Use a focused branch/worktree if the project workflow expects one.
- Do not expose secrets or private data.
- Do not mutate production data or deploy unless explicitly approved by project rules or the human owner.
- Capture exact validation commands and results.
- For UI changes, capture browser-visible evidence such as screenshots, traces, or exact manual QA steps.
- Update docs/changelog when the work is multi-issue, dependent, user/operator-visible, production-sensitive, or activation-gating.

Before claiming completion, return completion evidence:

- Branch, commit, and PR/MR/review artifact
- Files changed
- Validation commands/results
- Screenshots/traces/logs when applicable
- Safety and data-boundary notes
- Docs/changelog update or `N/A` reason
- Follow-up work discovered but kept out of scope
- Human decision requested
