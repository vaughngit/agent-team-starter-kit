# Linear Issue Guidance

Use this when adopting the Agent Team Starter Kit into a project that uses Linear for planning or execution tracking.

## Linear role

Linear owns live issue state, prioritization, assignment, labels, comments, blockers, and completion evidence. It should link to repo docs, PRs, changelog entries, and knowledge-base notes rather than duplicating everything.

## Recommended Linear issue shape

Create Linear issues from `templates/agent-issue-template.md` and keep these sections visible:

- Routing
- Problem / intent
- Acceptance criteria
- Scope boundaries
- Safety boundaries
- Source-of-truth links
- Validation gates
- Validation contract
- Handoff to implementation agent
- Completion evidence

## Labels or fields to consider

Use existing project conventions where possible. If adding labels, prefer simple labels such as:

- `agent-ready`
- `needs-shaping`
- `needs-human-approval`
- `needs-review`
- `docs-changelog-required`
- `ui-evidence-required`

## Completion evidence comment

When an agent says work is ready, require a final comment using `templates/completion-evidence-template.md`.

The comment should include:

- branch / commit / PR or review artifact
- validation commands and results
- docs/changelog impact or `N/A` reason
- screenshots/traces when applicable
- safety/data boundary
- follow-up issues
- human decision requested

## Safety

Do not put secret values, production credentials, customer data, private repo credentials, tokens, cookies, or private keys in Linear issues or comments. Use secret-manager pointer names only.
