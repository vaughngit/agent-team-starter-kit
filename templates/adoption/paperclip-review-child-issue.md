# Paperclip Review Child Issue

> **Status: experimental — preview.** See `paperclip-mr-review-regimen.md` for the full pattern.

Use this template to create a child Paperclip issue for one review lane.

```markdown
# [Review][<PARENT-ID>][<Lane>] <PR/MR title>

## Routing

- Parent issue:
- PR/MR:
- Reviewed SHA:
- Lane: correctness | operational | ui | docs
- Reviewer persona/agent:
- Status: backlog until intentionally activated
- Orchestrator (must be different from implementation owner):
- Implementation owner:
- Reviewer independence confirmed: yes/no
- PR/MR status mirror target: <comment URL or "orchestrator posts">

## Activation Rule

- `backlog` means this review lane is shaped but not active.
- `todo` means the reviewer may start.
- Do not move this issue to `todo` until the orchestrator intends to wake this reviewer.
- Do not bulk-activate sibling child issues; one lane at a time.
- Do not activate this issue if the named reviewer is the implementation owner of the PR/MR under review.

## Scope

Review only this lane's concern.

Lane focus:
- Correctness: behavior, scope, tests, data correctness, permissions, migration safety, mutation boundaries.
- Operational Risk: CI, deploy, infra, environment, dependency, config, rollback and smoke-check risk.
- UI Behavior: user-visible interaction, layout, accessibility-relevant markup, screenshots/traces.
- Docs / Operator Context: docs, runbooks, setup, policy, downstream activation notes.

## Out Of Scope

The reviewer must not:

- merge the PR/MR;
- mutate production data;
- expand the implementation scope;
- waive their own lane;
- mark the parent issue done;
- create unrelated follow-up work without naming it as out of scope.
- approve their own implementation work.

## Required Evidence

Return at least one independently re-openable artifact:

- tool-call IDs or agent-run transcripts;
- command output path (saved file, not just inline shell output);
- CI job URL;
- browser screenshot, trace, or screen recording path (stored where the human reviewer can open it; see "Evidence Storage" in the regimen doc);
- API response or log path;
- file citations (`path/file.ext:line` or `path/file.ext:line-line`);
- Paperclip comment or tool trace.

Reviewer output without at least one re-openable artifact is treated as `request_changes` regardless of the stated decision.

## Re-review Context (only if this is a subsequent iteration)

If this child issue is being re-reviewed after the implementation owner pushed a fix:

- Prior reviewed SHA: <sha>
- Prior decision: <approve|request_changes|blocked>
- Prior evidence: <link>
- New reviewed SHA: <sha>
- Files in the new diff that overlap this lane's scope: <list>

The reviewer must produce **fresh** evidence for the new SHA. Copying prior evidence forward is not allowed; the new SHA appears in the new evidence paths.

## Reviewer Instructions

1. Read the parent issue and the review matrix comment.
2. Inspect only the lane scope.
3. Verify against the reviewed SHA.
4. Run the smallest meaningful checks for this lane.
5. If your runtime is missing a tool the lane requires (e.g., browser automation for UI lane), do **not** improvise. Mark the decision `blocked` with the missing capability cited; the orchestrator routes the lane to a different reviewer.
6. If you are the implementation owner of the PR/MR, stop and return `blocked`; the orchestrator must route this lane to an independent reviewer.
7. Post a final decision comment using `paperclip-reviewer-output-contract.md`.
8. Include the `PR/MR Status Mirror` section. If you cannot update the PR/MR directly, tag the orchestrator and provide the exact status line to mirror.

## Completion

Decision must be one of:

- approve;
- request_changes;
- blocked.

If the decision is `request_changes` or `blocked`, cite the exact evidence and recommended next action so the implementation owner or orchestrator knows what to do.

The lane is not fully handed off until the decision is visible in both Paperclip and the PR/MR status mirror.
```
