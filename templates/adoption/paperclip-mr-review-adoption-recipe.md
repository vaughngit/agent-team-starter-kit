# Paperclip MR Review Regimen — First Adoption Recipe

> **Status: experimental — preview, not v1.** Companion to `paperclip-mr-review-regimen.md`.

This is a step-by-step recipe for a project that has read the regimen doc and wants to actually try it. Follow these steps in order; do not skip the pilot.

## Step 0: Decide whether you actually need this

Re-read the "When To Use This Pattern" section of `paperclip-mr-review-regimen.md`. If your project's PR/MR cadence is low, your changes are trivial, or you already have a working single-human-reviewer flow that catches problems, **don't adopt this**. Multi-agent review has real cost (orchestration overhead, agent budget, response latency). Adopt it only when the cost of merging the wrong thing exceeds those costs.

## Step 1: Read the supporting docs

In order:

1. `paperclip-mr-review-regimen.md` — concepts, principles, phased rollout, mapping to Paperclip primitives.
2. `paperclip-mr-review-matrix.md` — what the parent-issue review matrix looks like.
3. `paperclip-review-agent-setup.md` — what live reviewer agents must know before they are activated.
4. `paperclip-review-child-issue.md` — what each per-lane child issue looks like.
5. `paperclip-reviewer-output-contract.md` — what reviewer agents must produce.
6. `paperclip-project-instructions.md` — general Paperclip conventions you should already be following.

Then read the "How This Maps Onto Paperclip" and "What Paperclip Does NOT Give You" sections of the regimen doc again. If those gaps are deal-breakers for your project, stop and plan around them before continuing.

## Step 2: Create a tracking artifact outside Paperclip

Create a markdown file in your project's adoption notes (Second Brain, wiki, or repo docs — wherever your project tracks ongoing work). This file will be the canonical record of the pilot's status, gates, and findings.

Suggested structure:

```markdown
# MR Review Regimen — Pilot Tracking

Status: <one-line current state>

## Artifacts
- Regimen spec (your project's copy or pointer): ...
- Pilot target PR/MR: ...
- Paperclip pilot issue: <to be created>

## Gates
- Pre-pilot gate: <list any project-specific pre-conditions; verify before starting>
- Extraction gate: do not generalize the pilot's findings into project-wide policy until the retrospective is written

## Sequence
1. ...

## Findings Log
- <date> — <finding>
```

**Why outside Paperclip:** the pilot will produce findings you want to iterate on without churning a live Paperclip issue. The tracking file is durable; the Paperclip issue is execution state.

Do not confuse the tracking artifact with runtime reviewer context. If a reviewer needs a fact from this tracking file or an external knowledge base, copy the relevant durable fact into the repo, PR/MR, parent issue, or child issue before activating that reviewer.

## Step 3: Pick the pilot PR/MR

Pick one PR/MR in your project that:
- Exercises **at least two** of the default lanes (so the pilot tests coordination, not just one lane).
- Is **not the highest-risk** thing on your board — you don't want pilot mistakes to be expensive.
- Has a clear, identifiable implementation owner (so orchestrator independence can be enforced).

Do **not** pilot on more than one PR/MR at a time. Running two in parallel hides which problems come from the regimen versus which come from the changes.

## Step 4: Verify Paperclip state before activating

Read the implementation issue back via the Paperclip API. Confirm:

- The issue is in `in_review` or about to transition there.
- `executionState` and `executionPolicy` — note whether they are `null` (see Known Gotchas in the regimen doc).
- No dangling approval / confirmation references from prior workflow attempts. If any exist, resolve before starting.

Record this verification in the tracking file's Findings Log with the exact API outputs. This is your pre-pilot gate evidence.

## Step 5: Define roles explicitly

In the tracking file, write:

```markdown
## Pilot roles
- Implementation owner: <agent or human, by name>
- Orchestrator: <different agent or human, by name>
- Reviewer personas:
  - Correctness: <agent>
  - Operational: <agent>
  - UI: <agent>
  - Docs: <agent>
- Final approver: <human>
```

**Orchestrator must not be the implementation owner.** Verify this explicitly before continuing. The regimen's Design Principle #10 exists because role-definition ambiguity is the easiest failure mode to repeat — even when the principle is in the doc, adopters infer roles from job titles and miss the independence check.

**Reviewer personas must also be independent from the implementation owner.** If a project has too few reviewers to satisfy this, route the affected lane to a human or mark the lane blocked. Do not let the implementation owner approve their own PR/MR in a lane.

If your project's persona setup means the obvious orchestrator candidate is also the implementation owner, pick a different orchestrator (a peer agent, a supervisor agent, or a human). Do not proceed with the assignment if independence cannot be honored.

## Step 6: Update live reviewer agents

Before creating child issues, update the live Paperclip reviewer agents using `paperclip-review-agent-setup.md`.

Minimum install:

- each lane reviewer knows its lane scope;
- each lane reviewer knows the reviewer output contract;
- each lane reviewer knows it must include a PR/MR status mirror line;
- each lane reviewer knows to block when required context is missing from the repo, PR/MR, parent issue, or child issue;
- each lane reviewer knows not to approve its own implementation work.

Read the agents back from Paperclip and record verification in the tracking file. Do not assume updating these template files changed the agents that Paperclip will wake.

## Step 7: Draft the pilot Paperclip issue body in a file

Author the pilot issue body in a markdown file alongside the tracking file. Do **not** type it directly into the Paperclip UI.

Reasons:
- You will iterate on wording.
- You can review the body against the regimen's acceptance criteria before paste-time.
- The draft file becomes a durable record of what was pasted (which Paperclip's live state will drift from as the issue is worked).

Acceptance criteria the issue body must include:

- Pre-pilot gate cleared and recorded.
- Named orchestrator is accountable, **and is not the implementation owner of the PR/MR under review** (per Design Principle #10).
- Reviewer personas are independent from the implementation owner, or the affected lanes will be routed to a human/blocked.
- Child review issues will be created in `backlog`, activated one at a time.
- Review matrix posted on the parent.
- PR/MR status mirror posted on the git-provider PR/MR and updated after every lane state change.
- Each reviewer posts a decision comment meeting the Reviewer Output Contract with at least one re-openable evidence artifact.
- One iteration round walked if findings warrant; record _why_ if no findings warrant it (it is a meaningful signal either way).
- Retrospective appended to the tracking file.

## Step 8: Create the pilot issue in `backlog`

Create the Paperclip issue **in `backlog` first.** Do not move to `todo` immediately. `backlog` shapes the issue without waking any agent; `todo` is the activation handoff.

Record the assigned numeric ID in the tracking file. The ID will not match anything you predicted — Paperclip assigns it.

## Step 9: Activate the pilot

Once the orchestrator is ready to start (not before), move the pilot issue from `backlog` to `todo`. Paperclip may advance it to `in_progress` based on execution policy; that is expected.

The orchestrator's first job is to create the child review issues, **also in `backlog`**.

The orchestrator should also post the initial PR/MR status mirror on the git-provider PR/MR. The mirror can be a single rolling comment or whatever convention the project chooses, but it must be visible where the human merge decision happens.

## Step 10: Activate child issues one at a time

For each required lane:

1. Create the child issue with the correct title format: `[Review][<parent-id>][<Lane>] <PR/MR title>`.
2. Keep it in `backlog`.
3. Before moving it to `todo`, verify the reviewer persona has the tooling it needs (browser automation for UI, git/CI access for Operational, etc.).
4. Move to `todo`. The reviewer wakes.
5. Wait for the decision comment per the Reviewer Output Contract.
6. Confirm the decision includes a PR/MR status mirror line.
7. The orchestrator updates the parent matrix.
8. The orchestrator updates the PR/MR status mirror before treating the lane as handed off.
9. Repeat for the next lane.

**Do not bulk-activate.** All reviewers waking at once is how budget gets burned before findings can be observed.

## Step 11: Walk one iteration round if findings warrant

If any reviewer returns `request_changes`:

- The implementation owner pushes a fix.
- The orchestrator resets affected lanes per the Iteration Rule.
- Re-reviewed lanes produce fresh evidence (new SHA in the evidence paths).
- If after two iteration rounds findings are still unresolved, stop and escalate to the human, per the Done criterion.

If no findings warrant `request_changes` (every lane approves on first pass), record **why** in the tracking file. Either the regimen is producing trustworthy approvals, or reviewers are rubber-stamping. Both are real signals; both matter for the retrospective.

## Step 12: Handle emergency bypass if needed

Emergency bypass is allowed only by explicit human decision. Use it when waiting for all lanes would be riskier than proceeding, not when the regimen is merely inconvenient.

Record on the parent issue:
- human approver;
- incomplete, blocked, or stale lanes;
- reason for bypass;
- risk owner;
- rollback or mitigation plan;
- follow-up issues.

Count the bypass in the retrospective.

## Step 13: Hand off the approval packet to the human

Aggregate the lane decisions into a single short summary:

```markdown
## Approval Packet — <PR/MR>

Reviewed SHA: <sha>
Lanes:
- Correctness: approve — <child issue link>
- Operational: approve — <child issue link>
- UI: approve — <child issue link>
- Docs: waived (not operator-facing)

Conflicts: none.
Recommended next action: merge with notes.
Human decision requested: accept / request-changes / block.
```

The human reads this, opens whichever child issues they want to spot-check, and decides.

Post or update this approval packet on the PR/MR as well. The parent Paperclip issue can own the detailed record, but the PR/MR must show the current decision-ready state.

## Step 14: Write the retrospective

Append to the tracking file (and, if you maintain a project-level copy of the regimen spec, to that doc as well):

- Orchestrator effort (time, number of comments, number of state mutations).
- Where the matrix comment cost more upkeep than expected.
- Evidence reviewers tried to fabricate or skip, if any.
- Surprising Paperclip interactions (especially around `currentParticipant`, execution policies, child issue routing).
- Observed token/time cost per PR/MR.
- Pre-conditions a Phase 2 plugin would need to assume.
- Whether reviewer-agent capability text was sufficient or agents still missed the PR/MR mirror/context-boundary rule.
- Whether required review context was available in the repo/PR/MR/issues or had to be copied out of an external knowledge base.
- Whether the pattern should be expanded to more PR/MRs as-is, expanded with changes, or abandoned.

## Step 15: Decide on broader adoption

Only after the retrospective is written:

- **Adopt for all PR/MRs in this project** — go ahead, with the manual orchestration model. Phase 2 plugin is still optional and can wait until manual cost becomes the bottleneck.
- **Adopt with template changes** — make the changes to your project's local copy of these templates. Consider contributing the changes back to the starter kit if they are generic.
- **Abandon** — the cost outweighed the benefit. Record why in the retrospective.

Do not consider extracting any project-specific changes back into shared starter-kit templates until the pilot has clearly succeeded.

## Step 16: Promote the local copy to v1, or keep it preview

Do not remove preview language until the regimen doc's "Promotion To v1" criteria are met. If the pilot was inconclusive, keep the template marked preview and write down what evidence is still missing.

## Anti-patterns

Things to **not** do during the pilot:

- Do **not** activate multiple pilots at once. One PR/MR at a time.
- Do **not** assign the orchestrator role to the implementation owner. Even temporarily. Even "just to see what happens." This is the easiest failure mode to repeat.
- Do **not** assign the implementation owner as a review-lane reviewer for their own PR/MR.
- Do **not** commit raw browser captures, traces, or logs containing production data to the product repo. Link, don't commit.
- Do **not** require reviewer agents to mount a private knowledge base or second brain. Put required runtime context in the repo, PR/MR, parent issue, or child issue.
- Do **not** leave completed review lanes visible only in Paperclip. The PR/MR must show current lane state.
- Do **not** update only the templates and assume live Paperclip agents changed. Verify live agent configuration.
- Do **not** skip the retrospective. The retrospective is what makes the pilot worth running.
- Do **not** build a plugin before manual orchestration has been observed working end-to-end.
- Do **not** treat the templates as immutable. They are experimental. If your pilot surfaces a real flaw, fix the templates.
