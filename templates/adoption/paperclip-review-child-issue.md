# Paperclip Review Child Issue

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
- Orchestrator:
- Implementation owner:

## Activation Rule

- `backlog` means this review lane is shaped but not active.
- `todo` means the reviewer may start.
- Do not move this issue to `todo` until the orchestrator intends to wake this reviewer.

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

## Required Evidence

Return at least one independently re-openable artifact:

- command output path;
- CI job URL;
- browser screenshot/trace path;
- API response or log path;
- file citations;
- Paperclip comment or tool trace.

## Reviewer Instructions

1. Read the parent issue and review matrix.
2. Inspect only the lane scope.
3. Verify against the reviewed SHA.
4. Run the smallest meaningful checks for this lane.
5. Post a final decision comment using `paperclip-reviewer-output-contract.md`.

## Completion

Decision must be one of:

- approve;
- request_changes;
- blocked.

If the decision is `request_changes` or `blocked`, cite the exact evidence and recommended next action.
```

