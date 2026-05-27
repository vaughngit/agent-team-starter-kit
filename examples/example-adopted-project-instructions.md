# Example Adopted Project Instructions

This is an example of how a target project might look after adopting the Agent Team Starter Kit. Replace project-specific names and paths with the real project conventions.

## Agent operating model

This project uses agent issues as execution contracts. Agents should not start from vague chat context alone. They should work from a shaped issue with acceptance criteria, scope boundaries, safety boundaries, validation gates, and completion evidence requirements.

## Source of truth

- Human owner: intent, acceptance, and sensitive approvals
- Issue tracker: live work state, assignment, blockers, comments, completion evidence
- Repo: code, tests, `CHANGELOG.md`, `docs/decisions/`, implementation docs
- Knowledge base: broader planning and long-form rationale
- Secret manager: secrets and credential metadata
- PR/MR: diff, CI, review comments, and merge decision

## Required issue sections

Each agent issue should include:

- Problem / intent
- Acceptance criteria
- Scope boundaries
- Safety boundaries
- Validation gates
- Changelog/docs impact
- Completion evidence
- Human decision point

## Changelog/docs impact

Update `CHANGELOG.md` or `docs/decisions/` when work is multi-issue, dependent, production-sensitive, user/operator-visible, or required to decide whether downstream work can start.

For tiny one-off changes, write `Changelog/docs impact: N/A — <reason>` in the issue and completion evidence.

## Completion evidence

Before marking done, post:

- branch / commit / PR
- validation commands and results
- screenshots/traces if UI changed
- data/mutation boundary
- docs/changelog update or `N/A` reason
- follow-up issues
- human decision requested
