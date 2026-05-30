# Paperclip Review Agent Setup

> **Status: experimental — preview.** Companion to `paperclip-mr-review-regimen.md`.

Use this when creating or updating the live Paperclip agents that will serve as review-lane reviewers.

The regimen only works if the reviewer agents themselves know the handoff rules. Updating the MR review spec or parent issue is not enough.

## Required reviewer agents

Create or identify one independent reviewer persona per lane the project intends to use:

| Lane | Agent purpose |
|---|---|
| Correctness | Reviews behavior, scope, tests, data correctness, permissions, migration safety, and mutation boundaries. |
| Operational Risk | Reviews CI, deployment, infrastructure, environment, dependencies, config, rollback, and smoke-check risk. |
| UI Behavior | Reviews user-visible behavior, interaction, layout, accessibility-relevant markup, browser evidence, and adjacent-flow regressions. |
| Docs / Operator Context | Reviews docs, runbooks, setup, policy, reviewer context, downstream activation notes, and whether operators can safely run the change. |

Do not assign the implementation owner of the PR/MR as any review-lane reviewer for that PR/MR.

## Required orchestrator agent

Create or identify an orchestrator persona that is independent from the implementation owner. The orchestrator is the role that routes lanes, maintains the parent matrix and PR/MR mirror, aggregates the final approval packet, closes out the parent issue after the human merge decision, and runs the scheduled heartbeat control loop that keeps Paperclip-side team state converged.

Enable scheduled heartbeat on this orchestrator by default:

| Mode | Interval | Use when |
|---|---|---|
| Testing | 5 minutes | Proving the loop works. Not for steady-state operation. |
| Validation | 15 minutes | Recovery path has been observed working but is not yet trusted to run cold. |
| Mature default | 30 minutes | Webhooks, plugin reconciliation, and close-out have proven themselves. The heartbeat is the slow backstop, not the hot loop. |

The cadence is a progression, not a fixed default. If two consecutive heartbeats at the mature cadence find a missed close-out, stranded review lane, or blocked recovery path that the webhook/plugin loops should have converged, drop back to the validation cadence until the queue is stable.

Do not enable scheduled heartbeat on reviewer agents by default. Reviewers wake from child issue assignment, comments, or plugin lane activation. Implementation agents should also remain event-triggered unless their job is explicitly periodic monitoring.

## Capability text to install

Each reviewer agent should include the following behavior in its live Paperclip configuration, capability text, system instructions, or equivalent agent profile:

```text
You are an independent PR/MR review-lane agent. Review only your assigned lane against the reviewed SHA. Produce evidence-backed decisions using the project reviewer output contract. Decision must be approve, request_changes, or blocked.

Every final lane decision must include a PR/MR status mirror line. If you can update the git-provider PR/MR directly, post or update the mirror and link it in your Paperclip decision. If you cannot update the PR/MR directly, tag the orchestrator and provide the exact status line to mirror. The lane is not fully handed off until the decision is visible in Paperclip and on the PR/MR.

Do not rely on hidden local notes, private chat history, or a mounted external knowledge base. If required review context is missing from the product repo, PR/MR, parent Paperclip issue, or child review issue, return blocked and state exactly what context must be copied into one of those places.

Do not approve your own implementation work. If you are the implementation owner, return blocked and cite reviewer independence.
```

The orchestrator agent should include this behavior in its live Paperclip configuration, capability text, system instructions, or equivalent agent profile:

```text
You are the review orchestrator for structured PR/MR review. You enumerate required lanes, route one lane at a time, maintain the parent review matrix, mirror current lane state to the git-provider PR/MR, aggregate the final approval packet, and escalate conflicts or blockers without voting away reviewer decisions.

Your scheduled heartbeat is intentional. On each heartbeat, inspect your assigned actionable work, blocked recovery actions, stranded review lanes, delegated follow-ups, and merged-but-not-closed parent issues. Restore a live execution path when the safe next action is clear; otherwise leave the blocker in place with a named owner and next action. Record every state mutation with the evidence used and include a marker in the comment: `<!-- paperclip-heartbeat:<run-id>:<action>:<target-id> -->`.

Safe heartbeat mutations include closing a parent with complete merge evidence and current-SHA approvals, reactivating a same-SHA stranded lane with no final decision comment, delegating technical recovery, and repairing a PR/MR mirror from existing lane decisions. Unsafe mutations include approving a lane merely because recovery succeeded, closing a parent with missing lane decisions, or restoring an old child when a new SHA requires supersede.

After the human merges the linked PR/MR, mark the parent Paperclip implementation issue done and post a close-out comment containing the merge commit SHA, deploy info if applicable, and a link to the final approval packet. Do this within minutes of the merge; Paperclip close-out is not optional once the work has merged.

You must not orchestrate your own implementation work. If you are the implementation owner, return blocked and request a different orchestrator.
```

## Status mirror line

Reviewer decisions must include a line in this shape:

```text
<lane> <child issue>: <approve|request_changes|blocked> against <sha>; evidence: <link/comment/artifact>; next: <one-line next action>
```

The orchestrator or plugin copies that line into the PR/MR status mirror.

## Context boundary

Paperclip reviewers may use external planning notes only if the relevant durable content has been copied into one of these runtime-visible places:

- product repo docs at the reviewed SHA;
- PR/MR description or comments;
- parent Paperclip issue;
- child review issue.

If a project uses a private knowledge base, wiki, or second brain, treat it as planning input before activation. Do not require reviewer agents to mount or scrape it during review.

## Installation checklist

- [ ] Live reviewer agents exist for each required lane, or missing lanes are explicitly waived.
- [ ] Live orchestrator agent exists and is independent from the implementation owner.
- [ ] Live orchestrator scheduled heartbeat is enabled and interval is documented.
- [ ] Orchestrator heartbeat cadence matches the workflow state: 5 minutes during testing, 15 minutes during validation, 30 minutes as the mature default.
- [ ] Each reviewer agent has the capability text above, adapted to the project.
- [ ] The orchestrator agent has the close-out behavior above, adapted to the project.
- [ ] The orchestrator agent has the heartbeat convergence behavior above, adapted to the project.
- [ ] Reviewer agents are independent from likely implementation owners.
- [ ] Reviewer agents are event-triggered only unless a project-specific exception is recorded.
- [ ] UI reviewer has browser/screenshot tooling, or UI lane is routed to a human.
- [ ] Operational reviewer has access to CI/deploy metadata needed for its lane.
- [ ] Docs reviewer has repo/MR/issue context, not hidden external notes.
- [ ] A dry-run child issue confirms the reviewer output includes the PR/MR status mirror section.
- [ ] The parent matrix and PR/MR mirror are updated after the dry-run decision.
- [ ] A dry-run merge/close-out path confirms the orchestrator knows who signals merge and what evidence belongs in the close-out comment.

## Validation

After updating live agents, read each agent back from Paperclip and verify the installed behavior is present. Do not rely only on the template repo diff.
