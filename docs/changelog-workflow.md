# Changelog Workflow for Agent Teams

Use a changelog when agent work spans more than one issue, branch, reviewer, deployment, or session.

## Why this exists

Issue trackers are good at live state. Pull requests are good at diffs. Agent logs are good at execution detail.

A changelog gives future humans and agents the durable story:

- what changed
- why it mattered
- what evidence was reviewed
- what is still blocked
- whether the next issue is safe to activate

## When to require a changelog entry

Require one when:

- the work is part of a multi-issue initiative
- one issue depends on evidence from another
- production or customer data risk is involved
- a reviewer needs to understand prior agent decisions
- completion of this issue determines whether another agent should start
- the agent discovered follow-up work that should not be hidden in comments

For one-off docs or tiny changes, mark changelog `N/A` with a reason in the issue.

## Where changelog instructions belong

Put the changelog requirement in three places:

1. The issue template
   - Add a `Changelog or decision log` routing field.
   - Add a `Documentation/changelog gate` validation gate.
   - Add `Documentation/changelog update` and `Downstream activation decision` to completion evidence.

2. The agent/team operating docs
   - Tell supervisor and reviewer agents that the changelog is the durable narrative layer.

3. The issue handoff
   - Tell the implementation agent exactly which changelog entry to update or whether changelog is N/A.

## Agent-team pattern

1. Human gives a PRD, user story, bug, or rough vision.
2. Hermes or a supervisor agent creates the structured issue.
3. The issue says whether a changelog entry is required.
4. Implementation agent records commands, evidence, PR/MR link, and blockers.
5. Reviewer or supervisor updates the changelog before activating dependent work.
6. Human reviews the receipts and accepts, blocks, or redirects the next step.

## What good changelog entries include

- plain-English summary
- related issues and PRs/MRs
- validation evidence by gate
- safety and data boundaries
- open blockers
- follow-up work
- downstream activation decision
- done log

## Common mistake

Do not use a changelog as a dumping ground for every log line.

The changelog should be concise and durable. Raw command output, screenshots, traces, and full logs should live in the issue, PR/MR, or artifact storage; the changelog should summarize where they are and what they proved.
