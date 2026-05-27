# Agent Team Starter Kit project instructions

This project uses the Agent Team Starter Kit pattern for autonomous agent work.

## Core rule

Do not treat an agent claim as completion. Agent work is reviewable only when it returns receipts: acceptance criteria, scope boundaries, validation results, safety/data boundaries, review artifacts, docs/changelog impact, and a human decision point.

## Source-of-truth split

- Human owner: intent, risk tolerance, final acceptance, and approval for sensitive actions.
- Issue tracker / agent board: live task state, assignments, blockers, comments, and completion evidence.
- Repo: code, tests, repo-local docs, changelog, decision records, and implementation-adjacent requirements.
- PR/MR/code review: diffs, CI, review comments, and review artifacts.
- Knowledge base / external docs: broader planning, long-form rationale, cross-project notes, and non-secret reference material.
- Secret manager: secret values and credential metadata. Never put secrets in issues, commits, docs, logs, or examples.

## Agent issue contract

Before implementation starts, shape rough work into an issue that includes:

- problem / intent
- acceptance criteria
- in-scope and out-of-scope boundaries
- safety boundaries and mutation limits
- relevant source-of-truth links
- validation gates
- docs/changelog impact
- completion evidence requirements
- human decision point

Use the starter kit templates as the default structure:

- `templates/agent-issue-template.md`
- `templates/completion-evidence-template.md`
- `templates/changelog-entry-template.md`
- `templates/ui-evidence-validation-template.md` when UI behavior is involved

## Validation gates

Every agent issue should say which gates apply and what evidence is required:

- Static/code inspection
- Build/lint/test
- Backend/API validation
- UI/browser validation, including screenshots or traces when applicable
- Regression checks
- Deployment/smoke checks, when applicable
- Documentation/changelog update or `N/A` reason
- Completion evidence posted before done

## Changelog and docs impact

For multi-issue, dependent, production-sensitive, user-visible, operator-visible, or activation-gating work, update the project changelog or decision log.

Suggested defaults unless this project defines something else:

- `CHANGELOG.md` for user/operator-visible changes and multi-issue milestones
- `docs/decisions/` for architectural decisions
- `docs/change-management/` for rollout, validation, rollback, or downstream activation notes
- `docs/requirements/` for repo-safe mini-PRDs or implementation-adjacent requirements

For one-off tiny changes, mark changelog/docs impact `N/A` with a reason.

## Completion evidence

An implementation agent must return:

- branch / commit / PR or review artifact
- exact validation commands and results
- artifact links or paths for screenshots, traces, logs, or reports
- safety and data-boundary notes
- docs/changelog update or `N/A` reason
- follow-up issues for related work discovered out of scope
- clear human decision requested: accept, request changes, approve merge/deploy/activation, create follow-up, or stop

## Boundaries

- Do not expand scope because a related issue was discovered.
- Do not merge, deploy, mutate production data, rotate credentials, or activate downstream work unless project rules explicitly allow it or the human approves.
- Do not put secrets, private customer data, credentials, private keys, tokens, or sensitive production data in issues, docs, examples, commits, or logs.
