# Paperclip MR Review Regimen

Status: experimental template

Use this template when a Paperclip-backed project wants PR/MR review to trigger structured multi-agent review instead of treating "PR opened" or "MR opened" as completion.

This is a coordination pattern. It does not replace project-specific delivery rules, human approval, CI, code review, or deployment checks.

## Core Rule

`in_review` means:

1. An implementation artifact exists, usually a PR or MR.
2. Required review lanes are created as child Paperclip issues.
3. Reviewer agents return evidence-backed decisions.
4. The orchestrator aggregates results into a human-readable approval packet.
5. A human or designated owner makes the final merge, deploy, or acceptance decision.

Opening a PR/MR starts review. It is not completion.

## Design Principles

1. **Few lanes, not many.** Use the smallest set of reviewer lanes that covers the risk.
2. **Evidence must be verifiable.** A reviewer decision needs re-openable receipts: CI URLs, command output, screenshots/traces, logs, file citations, API responses, or Paperclip comments.
3. **Reviewers may not waive their own lane.** Waivers belong to the orchestrator or human owner and must include a reason.
4. **Approval is tied to a reviewed SHA.** A review approves a specific commit or head SHA, not the branch forever.
5. **New commits reset affected lanes.** Re-review lanes whose scope overlaps the new diff.
6. **Disagreement escalates.** The orchestrator does not vote away a blocking reviewer finding.
7. **Reviewer budget is explicit.** Name expected lanes and iteration limits before starting.
8. **Auto-merge stays off by default.** The regimen makes human approval easier; it does not silently merge production-impacting work.
9. **Orchestrator independence.** The orchestrator must not be the implementation owner of the PR/MR under review. The implementation owner responds to findings; the orchestrator creates and routes review lanes and aggregates decisions.

## Default Lanes

| Lane | Trigger | Reviewer focus |
|---|---|---|
| Correctness | Any code change unless the PR/MR is docs-only. | Behavior, scope, tests, data correctness, permissions, migration safety, mutation boundaries. |
| Operational Risk | CI, deployment, infra, environment, dependency, or config changes. | Pipeline behavior, release/deploy risk, rollback path, smoke checks, runner/environment assumptions. |
| UI Behavior | User-visible UI, interaction, accessibility-relevant markup, or visual state changes. | Real browser interaction, screenshots/traces, responsive behavior, adjacent-flow regressions. |
| Docs / Operator Context | Operator-facing behavior, runbooks, setup, policy, or activation semantics change. | Accuracy, downstream activation notes, docs match what changed. |

Do not create all lanes by default. Create only the lanes required for the current PR/MR. Record waived lanes and reasons in the review matrix.

## Lifecycle

```text
implementation issue in_progress
-> PR/MR opened
-> parent issue in_review
-> orchestrator creates child review issues in backlog
-> orchestrator activates lanes intentionally
-> reviewers post evidence-backed decisions
-> orchestrator updates review matrix
-> human receives approval packet
-> merge/deploy/acceptance only after approval
```

## Orchestrator Role

The orchestrator:

- Confirms the implementation artifact and reviewed SHA.
- Chooses required lanes.
- Creates child review issues.
- Keeps inactive lanes in `backlog`.
- Moves one lane to `todo` only when ready to wake that reviewer.
- Maintains the parent review matrix.
- Aggregates reviewer decisions.
- Escalates conflicts and blockers.
- Records waivers and reasons.
- Produces the final approval packet.

The orchestrator must not be the implementation owner of the PR/MR under review.

## Implementation Owner Role

The implementation owner:

- Opens the PR/MR or equivalent artifact.
- Provides the implementation summary and validation evidence.
- Responds to reviewer findings.
- Pushes fixes when requested.
- Does not create, route, waive, or close review lanes for their own work.

## Child Review Issue Rule

Create one child Paperclip issue per required review lane.

Recommended title format:

```text
[Review][<parent-issue-id>][<lane>] <PR/MR title>
```

Create child issues in `backlog`. Move a child issue to `todo` only when the orchestrator intends to activate that reviewer.

## Iteration Rule

When the implementation owner pushes a new commit:

1. Compare the new head SHA to the last reviewed SHA.
2. Reset affected lanes to pending or `todo`/`in_review` according to the project workflow.
3. Require fresh evidence for affected lanes.
4. Keep unaffected lane approvals only if their reviewed SHA and scope remain valid.

## Completion Rule

The parent issue is not ready for final human approval until:

- required lanes are `done` against the current reviewed SHA, or explicitly waived with a reason;
- blockers and conflicts are resolved or escalated;
- the approval packet links to the reviewer decisions and evidence;
- the human decision point is clear.

