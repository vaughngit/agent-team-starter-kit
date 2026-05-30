# Paperclip Agent Prompt Templates

This directory holds the **agent behavior layer** of the Paperclip MR review regimen — the system-prompt content adopters install in each live reviewer agent and in the CEO/orchestrator. The two adjacent layers are:

- **Process layer** — `templates/adoption/paperclip-mr-review-regimen.md` (how review works as a system).
- **Adoption layer** — `templates/adoption/paperclip-mr-review-adoption-recipe.md`, Step 6 (how to install and update reviewer agents on a new project's Paperclip).

The behavior layer is *how each live agent should behave once it wakes*, expressed as prompt content rather than process documentation.

## Status: seeded from the ClearFi pilot

This directory ships with reviewer prompts genericized from the ClearFi pilot's four live reviewer agents (Correctness, Operational, UI/QA, Docs), exported and stripped of project-specific identifiers on 2026-05-30. It also includes an orchestrator prompt seed that captures the CEO heartbeat control-loop lesson from the same pilot. Each file's source-note header documents the provenance. These reflect *one* pilot's working set, not a universal contract.

**Known gaps vs the regimen's Reviewer Agent Behavior Contract.** The live ClearFi prompts predate the contract's formalization in this regimen, so the seeded files do not yet encode every rule from "Reviewer Agent Behavior Contract" in `paperclip-mr-review-regimen.md`. Tracked as a follow-up on the source agents:

- Posting final decisions to **both** the MR comment thread and the child issue is not explicit (only the PR/MR status mirror line is).
- The parseable `Decision: approve | request_changes | blocked | waived` line is not stated as a required output.
- The budget-extension-request protocol is implicit ("respect budget") rather than spelled out.
- Self-review block / recusal is not stated.
- SHA-scoped re-review behavior (the reviewer waits for a fresh SHA-scoped child issue instead of re-evaluating the stale one) is not mentioned.
- The runtime context boundary and child-issue-only status-mutation rule are not fully stated.

**When you adopt these prompts**, layer the missing rules from `templates/adoption/paperclip-mr-review-regimen.md` ("Reviewer Agent Behavior Contract") on top of the seeded content. The seeds give you voice, scope, and lane-specific lenses validated by real review operation; the regimen gives you the contract rules that drift exposed.

**If you adapt or replace these prompts**, follow the same genericize-and-contribute-back loop: export your project's live prompts, strip project-specific identifiers, save here as `<lane>-reviewer-prompt.md` (replacing or sitting alongside the ClearFi seeds), and contribute back as a PR so the next adopter has multiple working examples to compare.

## Files in this directory

- `correctness-reviewer-prompt.md` — behavior, regression, data integrity, auth, migrations, tests, edge cases.
- `operational-reviewer-prompt.md` — deployability, migration safety, runtime config, rollback, observability, blast radius, artifact hygiene.
- `ui-qa-reviewer-prompt.md` — UI/QA with browser/runtime evidence, artifact retention, workflow/visual/auth-routing lenses, non-destructive testing.
- `docs-reviewer-prompt.md` — operator clarity, source-of-truth alignment, evidence linkage, safety language, completion discipline.
- `orchestrator-prompt.md` — CEO/orchestrator heartbeat control loop, lane routing, MR mirror upkeep, recovery delegation, and post-merge close-out.
- `README.md` — this file.

Naming is suggested, not required — match what your project actually deploys.

## What each prompt should encode

At minimum:

1. **Lane and scope** — which files, paths, and concerns this lane owns; which it does not. Be explicit about the boundary, not aspirational.
2. **The Reviewer Agent Behavior Contract** from the regimen, paraphrased for the agent's voice: trigger, discovery (read at the recorded SHA), output on both surfaces (MR comment *and* child issue) with a parseable `Decision:` line, status update on the child issue only, budget-extension protocol, no self-re-review on a new SHA.
3. **The Reviewer Output Contract format** — see `paperclip-reviewer-output-contract.md`. Don't inline the entire contract; link to it and restate the load-bearing rules.
4. **Project-specific runtime context** the reviewer needs — local conventions, deploy notes, test-run commands. Keep it short and link to repo docs rather than inline-copying content that will drift.
5. **Escalation rules** — when to block, when to request a budget extension, when to tag a human.

For orchestrator prompts, also encode:

- Scheduled heartbeat is intentional and enabled by default.
- The orchestrator checks assigned work, stranded review lanes, blocked recovery actions, delegated follow-ups, stale blockers, and merged-but-not-closed parent issues.
- The orchestrator does not review its own implementation work, replace reviewer judgment, merge code, or guess external state without evidence.
- The orchestrator records every state mutation with the evidence used.

## Avoid

- Embedding live secrets, tokens, or credentials in prompts.
- Embedding project-specific issue IDs as if they were universal references.
- Long inline copies of repo docs that will drift out of date; link instead.
- Mounting external knowledge bases (private wikis, second brains) as runtime context — the regimen explicitly requires runtime context to come from the repo, PR/MR, parent issue, or child issue.
- Implying the agent should self-poll for work, mutate the parent issue, or re-review a stale child issue when a new SHA arrives.
- Disabling the CEO/orchestrator heartbeat without recording an explicit reason. The heartbeat is the Paperclip team control loop; reviewer self-polling is the anti-pattern, not orchestrator convergence.

## After installing or updating a prompt

Read the agent configuration back from Paperclip and record the verification in your project's pilot tracking file. Editing a `.md` file in this directory does not update any live agent — see `paperclip-mr-review-adoption-recipe.md`, Step 6 for the install/update procedure.
