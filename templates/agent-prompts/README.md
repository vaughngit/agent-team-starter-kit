# Reviewer Agent Prompt Templates

This directory holds the **agent behavior layer** of the Paperclip MR review regimen — the system-prompt content adopters install in each live reviewer agent. The two adjacent layers are:

- **Process layer** — `templates/adoption/paperclip-mr-review-regimen.md` (how review works as a system).
- **Adoption layer** — `templates/adoption/paperclip-mr-review-adoption-recipe.md`, Step 6 (how to install and update reviewer agents on a new project's Paperclip).

The behavior layer is *how each live reviewer agent should behave once it wakes*, expressed as prompt content rather than process documentation.

## Status: empty by design

This directory ships intentionally empty. Prompt content is most useful when seeded from a working pilot's live agents, not invented from a spec. A starter-kit prompt that hasn't been observed working in a real review will drift from reality faster than it can be corrected.

**If you are adopting the regimen for the first time:**

1. Read the **Reviewer Agent Behavior Contract** section of `paperclip-mr-review-regimen.md`. That is the minimum each prompt must encode.
2. For each lane your project uses, write a prompt that combines the regimen's behavior contract with your project's specific scope rules (file paths, deploy considerations, UI tooling, test conventions).
3. Save it here as `<lane>-reviewer-prompt.md` so your future-self and team can iterate on it alongside the regimen.

**If your pilot has produced reviewer agents that work well:**

- Export their prompts from Paperclip (one per agent).
- Genericize them — strip project-specific paths, issue IDs, deploy info, customer names, repo URLs — and contribute them back to the starter kit so the next adopter inherits proven content.

## Expected files when populated

```
agent-prompts/
  correctness-reviewer-prompt.md
  operational-reviewer-prompt.md
  ui-qa-reviewer-prompt.md
  docs-reviewer-prompt.md
```

Naming is suggested, not required — match what your project actually deploys.

## What each prompt should encode

At minimum:

1. **Lane and scope** — which files, paths, and concerns this lane owns; which it does not. Be explicit about the boundary, not aspirational.
2. **The Reviewer Agent Behavior Contract** from the regimen, paraphrased for the agent's voice: trigger, discovery (read at the recorded SHA), output on both surfaces (MR comment *and* child issue) with a parseable `Decision:` line, status update on the child issue only, budget-extension protocol, no self-re-review on a new SHA.
3. **The Reviewer Output Contract format** — see `paperclip-reviewer-output-contract.md`. Don't inline the entire contract; link to it and restate the load-bearing rules.
4. **Project-specific runtime context** the reviewer needs — local conventions, deploy notes, test-run commands. Keep it short and link to repo docs rather than inline-copying content that will drift.
5. **Escalation rules** — when to block, when to request a budget extension, when to tag a human.

## Avoid

- Embedding live secrets, tokens, or credentials in prompts.
- Embedding project-specific issue IDs as if they were universal references.
- Long inline copies of repo docs that will drift out of date; link instead.
- Mounting external knowledge bases (private wikis, second brains) as runtime context — the regimen explicitly requires runtime context to come from the repo, PR/MR, parent issue, or child issue.
- Implying the agent should self-poll for work, mutate the parent issue, or re-review a stale child issue when a new SHA arrives.

## After installing or updating a prompt

Read the agent configuration back from Paperclip and record the verification in your project's pilot tracking file. Editing a `.md` file in this directory does not update any live agent — see `paperclip-mr-review-adoption-recipe.md`, Step 6 for the install/update procedure.
