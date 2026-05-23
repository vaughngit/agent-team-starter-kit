# Agent Issue Template: Execution Contract

Use this template when creating work for an autonomous coding agent or agent team.

The goal is to make the work reviewable. A good issue should let the agent act without guessing and let the human reviewer inspect evidence without replaying the entire task.

```markdown
# <ISSUE-ID>: <Title>

## Routing

- Project/company:
- Tracker/project:
- Repo/workspace:
- Base branch/ref:
- Status: backlog | todo | in_progress | in_review | blocked | done | cancelled
- Assigned persona/agent: supervisor | coding agent | reviewer | QA | TBD
- Work type: bug | feature | infra | docs | research | ops
- Priority: critical | high | medium | low
- Effort: XS | S | M | L | XL
- Source of truth:
- Project README / agent contract:
- Changelog or decision log:

## Activation rule

- `backlog` means passive inventory.
- `todo` means activation handoff: an assigned agent is expected to start.
- Do not move to `todo` until the issue is shaped enough for action.

## Problem / intent

What user pain, product gap, operational failure, or engineering risk is this addressing?

## User-visible symptom or story

For bugs:
- Symptom:
- Expected behavior:
- Reproduction steps:

For features:
- As a <user/persona>, I want <capability>, so that <outcome>.

## Acceptance criteria

- [ ] The observable outcome is explicit and testable.
- [ ] The scope is one logical change.
- [ ] The expected behavior is stated in user-visible terms.
- [ ] Edge cases or non-goals are named.

## Scope boundaries

In scope:
- ...

Out of scope:
- ...

If the agent finds related issues, it should document them as follow-up work instead of expanding this task without approval.

## Safety boundaries

- Data that must not be mutated:
- Secrets/credentials policy:
- Safe fixture or test-data plan:
- Human approval required before:
- Rollback or stop condition:

## Technical notes / constraints

- Relevant files/components:
- Known gotchas:
- Migration/deploy needed? yes/no/unknown
- Required services or local environment:
- Credential pointer, if required, without secret values:

## Validation gates

Trust requires evidence, not just an agent claim.

- [ ] Intent gate — acceptance criteria are explicit and testable.
- [ ] Scope gate — implementation is one logical change from the correct base branch.
- [ ] Workspace gate — correct repo/workspace is attached and checked out.
- [ ] Automated gate — relevant build/test/lint command(s) run and results captured.
- [ ] Data/API gate — backend behavior is validated with safe API calls, fixtures, or read-only inspection when applicable.
- [ ] UI/presentation gate — visible behavior is validated with browser automation, screenshots, traces, or exact manual QA steps when applicable.
- [ ] Regression gate — likely adjacent breakage checked.
- [ ] Review gate — PR/MR/review artifact opened when required.
- [ ] Documentation/changelog gate — relevant tracker, docs, or decision log updated when needed.
- [ ] Completion gate — completion evidence posted before work is marked done.

## Validation contract

Fill the layers that apply. Mark non-applicable layers `N/A` with one sentence.

### Static/code inspection

- Files expected to change:
- Grep/file-by-file checks:
- Expected result:

### Automated validation

- Command(s):
- Expected output/status:
- Artifact path, if any:

### Backend/API validation

- Command(s):
- Expected output/status:
- Required auth/secret pointer, if any:
- Mutation boundary:

### UI/browser validation

- Browser path/URL:
- Existing fixture or temporary local harness:
- Fixture data used and mutation boundary:
- API routes intercepted/mocked, if any:
- Interaction sequence:
- Expected visible state/copy/dropdown/disabled state/color/contrast:
- Screenshot/trace artifact required: yes/no
- Artifact paths or PR/MR upload links:
- Temporary harness removed before commit: yes/no/N/A

### Deployment validation

- Environment:
- Smoke command(s):
- Expected health/version/result:

### Human-only QA, if unavoidable

- Why automation is insufficient:
- Exact steps a human should run:
- What evidence should be captured:

## Implementation contract

If this changes code and should reach production:

1. Read the project-specific delivery workflow.
2. Start from the required base branch.
3. Create a focused branch/worktree.
4. Implement the smallest logical change.
5. Run targeted validation and capture exact commands/results.
6. Open a PR/MR/review artifact if required.
7. Move the issue to `in_review` when implementation is ready for human review.
8. Do not merge/deploy unless project rules explicitly allow it.
9. Do not mark done until review and completion evidence are recorded.

## Handoff to implementation agent

```text
@<agent> implement <ISSUE-ID> via the project-specific delivery workflow.
Acceptance criteria are in this issue.
Required validation: <commands, browser checks, screenshots/traces, artifact expectations>.
PR/MR opening is approved: yes/no.
Do not mark done until completion evidence is logged.
```

## Completion evidence

- Branch:
- Commit(s):
- PR/MR/review artifact:
- Validation commands/results:
- API/data evidence:
- UI/browser evidence:
- Screenshot/trace artifacts:
- Safety evidence / mutation boundary:
- Human-only QA exception, if any:
- Deploy/cache/migration note:
- Documentation/changelog update:
- Follow-up issues:
- Done log:
```
