# Paperclip Project Instructions

Use these instructions when adopting the Agent Team Starter Kit into a Paperclip-backed project or board.

## Paperclip role

Paperclip is the live agent board and execution tracker. It owns:

- issue state
- assignment
- agent comments
- blockers
- completion evidence
- review handoff state

Paperclip does not replace the repo, project docs, secret manager, or human approval. Agents should link to those sources instead of copying everything into the issue.

## Issue creation

When a local assistant or supervisor creates a Paperclip issue, shape it as an execution contract using `templates/agent-issue-template.md`.

Every Paperclip issue should include:

- project/company
- repo/workspace and base ref
- source-of-truth links
- project README / `AGENTS.md` / runbook
- problem or intent
- acceptance criteria
- scope boundaries
- safety boundaries
- validation gates
- changelog/docs impact
- completion evidence requirements
- human decision point

## Activation rule

- `backlog` means passive inventory.
- `todo` means an assigned agent may start.
- Do not move an issue to `todo` until the issue has enough context for action.
- Do not let an implementation agent redefine scope without a human or supervisor decision.

## Agent completion

Before moving an issue to review or done, require completion evidence:

- branch / commit / PR or review artifact
- exact validation commands and results
- UI screenshots/traces when applicable
- data and mutation boundary
- docs/changelog update or `N/A` reason
- follow-up issues for out-of-scope discoveries
- requested human decision

## Optional: MR review regimen

For projects that need structured multi-agent PR/MR review, use the experimental MR review templates:

- `templates/adoption/paperclip-mr-review-regimen.md`
- `templates/adoption/paperclip-mr-review-matrix.md`
- `templates/adoption/paperclip-review-child-issue.md`
- `templates/adoption/paperclip-reviewer-output-contract.md`

Do not treat PR/MR creation as completion. PR/MR creation starts the review regimen.

Important coordination rule: the review orchestrator must not be the implementation owner of the PR/MR under review. The implementation owner responds to review findings; the orchestrator creates and routes review lanes, tracks evidence, and aggregates the approval packet.

## Safety

Never put secret values, credentials, private keys, tokens, cookies, or sensitive production/customer data in Paperclip issue text or comments. Use secret-manager pointer names only.
