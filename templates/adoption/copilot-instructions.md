# GitHub Copilot instructions

Use the Agent Team Starter Kit pattern for agentic engineering work in this repository.

Agent work should be shaped as an execution contract before implementation. Prefer small, reviewable changes with explicit acceptance criteria, scope boundaries, safety boundaries, validation gates, and completion evidence.

When suggesting or implementing changes:

- Follow existing project instructions in `AGENTS.md`, README, and local runbooks.
- Keep work within the issue scope.
- Do not introduce secrets, credentials, tokens, private keys, customer data, or sensitive production data.
- Do not expand scope for related issues; document follow-up work instead.
- Include or update tests when relevant.
- Include docs/changelog updates for user-visible, operator-visible, multi-issue, dependent, production-sensitive, or activation-gating changes.
- For UI changes, require visible validation evidence such as screenshots, traces, or exact manual QA steps.

Completion evidence should include:

- Summary of outcome
- Files changed
- Tests/build/lint/validation commands and results
- PR/MR/review artifact, if applicable
- Docs/changelog impact or `N/A` reason
- Safety/data-boundary note
- Human decision requested
