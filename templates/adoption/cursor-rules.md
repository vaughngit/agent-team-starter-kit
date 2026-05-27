# Cursor rules: Agent Team Starter Kit

Use this rule when Cursor or an AI coding assistant is asked to perform agentic work in this project.

- Convert vague requests into an execution contract before coding.
- Require acceptance criteria, scope boundaries, safety boundaries, validation gates, and completion evidence.
- Read project-local instructions first: `AGENTS.md`, README, runbooks, and issue templates.
- Preserve the project source-of-truth split: issue tracker for live state, repo for code/docs/changelog, knowledge base for broader rationale, secret manager for secrets, human owner for final acceptance and sensitive approvals.
- Do not include secrets, credentials, customer data, private keys, tokens, cookies, or sensitive production data in code, docs, examples, commits, or logs.
- Keep changes small and reviewable.
- If related work is discovered, create or recommend a follow-up issue instead of expanding scope.
- Run relevant validation and record exact commands/results.
- For UI changes, include browser-visible evidence.
- Update changelog or decision log when the work is multi-issue, dependent, user/operator-visible, production-sensitive, or needed for downstream activation.
- Return receipts before marking work complete.
