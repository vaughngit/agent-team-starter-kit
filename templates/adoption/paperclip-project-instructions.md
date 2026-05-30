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

If the project uses a private knowledge base, wiki, or second brain, Paperclip may link to it as planning context, but reviewer agents must not require it at runtime. Anything required for implementation or review belongs in the product repo, PR/MR, parent Paperclip issue, or child review issue before activation.

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

For projects that need structured multi-agent PR/MR review, use the experimental MR review templates (status: preview, not v1 — see individual file headers):

- `templates/adoption/paperclip-mr-review-regimen.md` — concepts, principles, mapping to Paperclip primitives, phased rollout, known gotchas.
- `templates/adoption/paperclip-mr-review-adoption-recipe.md` — step-by-step recipe for your first adoption. **Start here if you are actually planning to implement.**
- `templates/adoption/paperclip-mr-review-matrix.md` — parent issue matrix template.
- `templates/adoption/paperclip-review-agent-setup.md` — live reviewer-agent capability and setup guidance.
- `templates/adoption/paperclip-review-child-issue.md` — per-lane child issue template.
- `templates/adoption/paperclip-reviewer-output-contract.md` — required reviewer decision/evidence format.

Do not treat PR/MR creation as completion. PR/MR creation starts the review regimen.

Important coordination rule: the review orchestrator must not be the implementation owner of the PR/MR under review. The implementation owner responds to review findings; the orchestrator creates and routes review lanes, tracks evidence, and aggregates the approval packet. State role assignments explicitly by relationship to the PR/MR, not by job title — title-based inference is the easiest failure mode to repeat.

Important visibility rule: Paperclip issue state is not enough. The orchestrator, reviewer, or plugin must maintain a PR/MR status mirror on the git-provider review surface. Each reviewer decision must include a mirror-ready status line, and a lane is not fully handed off until both Paperclip and the PR/MR reflect the lane state.

Important agent-setup rule: changing these repo templates does not automatically update live Paperclip agents. When adopting the regimen, update the reviewer agents' live capability/instruction text using `paperclip-review-agent-setup.md`, then read the agents back from Paperclip to verify the rule is present.

Important heartbeat rule: Paperclip team workflows should run a scheduled CEO/orchestrator heartbeat. This is the control loop for Paperclip-side convergence: stranded lanes, blocked recovery issues, delegated follow-ups, stale blockers, and parent close-out drift. Use 15 minutes as the normal default, 5 minutes for pilots/incidents/unstable automation, and 30 minutes for mature low-traffic projects. Keep reviewer agents event-triggered unless a specific reviewer role is intentionally periodic.

## Safety

Never put secret values, credentials, private keys, tokens, cookies, or sensitive production/customer data in Paperclip issue text or comments. Use secret-manager pointer names only.
