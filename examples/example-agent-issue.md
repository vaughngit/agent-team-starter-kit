# Example Agent Issue: Add confirmation before a sensitive import action

This is a synthetic example. It is intentionally generic and does not describe a real app, real customer data, or private infrastructure.

## Routing

- Project/company: Example App
- Tracker/project: Product Backlog
- Repo/workspace: `example-app`
- Base branch/ref: `main`
- Status: backlog
- Assigned persona/agent: coding agent
- Work type: feature
- Priority: medium
- Effort: S
- Source of truth: this issue
- Project README / agent contract: `README.md`, `AGENTS.md`
- Changelog or decision log: `docs/changelog.md`

## Activation rule

Keep this issue in `backlog` until the project workspace is attached and the coding agent is assigned. Move to `todo` only when work should begin.

## Problem / intent

Users can trigger a sensitive import action without clearly acknowledging the target account. This creates avoidable review risk and makes it harder for a human to trust the action.

## User-visible story

As a user importing data, I want the final import button to stay disabled until I acknowledge the target account, so that I do not accidentally import data into the wrong place.

## Acceptance criteria

- [ ] The dialog displays the target account name before final import.
- [ ] The final import button is disabled before acknowledgement.
- [ ] Checking the acknowledgement box enables the final import button.
- [ ] The change uses synthetic fixture data for validation.
- [ ] The PR includes screenshots showing context, disabled state, and enabled state.

## Scope boundaries

In scope:
- Add visible target-account context.
- Add acknowledgement gate.
- Add UI/browser validation evidence.

Out of scope:
- Changing import parsing behavior.
- Changing backend data model.
- Importing real customer data during validation.

## Safety boundaries

- Data that must not be mutated: production data
- Secrets/credentials policy: no secret values in issue, logs, screenshots, or PR
- Safe fixture or test-data plan: synthetic account named `Fixture Account`
- Human approval required before: any production test import
- Rollback or stop condition: validation requires real production data

## Technical notes / constraints

- Relevant files/components: import dialog component and its tests
- Known gotchas: final button state must be driven by acknowledgement, not just preview success
- Migration/deploy needed? no migration expected
- Required services or local environment: local frontend dev server
- Credential pointer: N/A

## Validation gates

- [ ] Intent gate — acceptance criteria are explicit and testable.
- [ ] Scope gate — implementation is one logical UI change.
- [ ] Workspace gate — correct repo/workspace is attached.
- [ ] Automated gate — relevant build/test command(s) run and results captured.
- [ ] UI/presentation gate — browser screenshots prove the required states.
- [ ] Regression gate — existing import happy path still works with fixture data.
- [ ] Review gate — PR/MR includes summary and evidence.
- [ ] Completion gate — issue comment includes completion evidence before done.

## UI/browser validation

- Browser path/URL: local fixture route or review URL
- Existing fixture or temporary local harness: temporary local-only route allowed
- Fixture data used and mutation boundary: synthetic one-row import and `Fixture Account`
- API routes intercepted/mocked, if any: preview/import API may be intercepted to avoid real mutation
- Interaction sequence:
  1. Open import dialog.
  2. Upload synthetic sample file.
  3. Confirm target account text is visible.
  4. Confirm final import button is disabled.
  5. Check acknowledgement box.
  6. Confirm final import button is enabled.
- Expected visible state: target account label, acknowledgement checkbox, disabled/enabled button states
- Screenshot/trace artifact required: yes
- Artifact paths or PR/MR upload links: to be filled by agent
- Temporary harness removed before commit: yes

## Handoff to implementation agent

```text
@coding-agent implement this issue via the project-specific delivery workflow.
Acceptance criteria are in this issue.
Required validation: targeted frontend tests plus browser screenshots for target account context, disabled button before acknowledgement, and enabled button after acknowledgement.
PR/MR opening is approved: yes.
Do not mark done until completion evidence is logged.
```

## Completion evidence

- Branch:
- Commit(s):
- PR/MR/review artifact:
- Validation commands/results:
- UI/browser evidence:
- Screenshot/trace artifacts:
- Safety evidence / mutation boundary:
- Human-only QA exception, if any:
- Documentation/changelog update:
- Follow-up issues:
- Done log:
