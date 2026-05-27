# Agent Team Operating Model

This is a lightweight operating model for reviewable autonomous agent work.

## Roles

### Human / owner

- Provides product intent and risk tolerance.
- Identifies or confirms the source of truth for requirements, approval, and sensitive boundaries.
- Approves scope, credentials, production mutations, merge, and deploy when required.
- Reviews evidence before accepting work.

### Local assistant / operator

- Helps turn intent into mini-PRDs and structured issues.
- Finds source-of-truth docs and context.
- Uses board-authorized credentials, when explicitly configured, to create or update issues as the board user/operator.
- Updates trackers and handoff notes.
- Does not silently turn vague intent into production changes.

### Supervisor / CTO / planner agent

- Shapes work into implementation-ready issues.
- Confirms repo/workspace wiring.
- Chooses the right implementation path.
- Keeps lifecycle state coherent: backlog, todo, in_progress, in_review, done, blocked.
- Maintains the changelog or decision log when work spans multiple issues, review steps, or downstream activation decisions.

### Review orchestrator

- Routes review lanes without serving as the implementation owner for the PR/MR under review.
- Creates or verifies child review issues, reviewer assignments, reviewed SHA, and evidence requirements.
- Maintains the parent review matrix or equivalent tracker summary.
- Maintains the PR/MR status mirror so the code-review surface shows current lane state.
- Aggregates reviewer decisions into a human decision packet without voting away blocking findings.

### Coding agent

- Implements one focused issue.
- Starts from the required base branch.
- Keeps scope tight.
- Runs validation and records exact evidence.
- Opens a branch / PR / MR as the review handoff.

### Reviewer / QA agent

- Checks spec compliance and evidence quality.
- Verifies screenshots, traces, logs, tests, and review notes.
- Flags gaps instead of accepting vague claims.
- Reviews only its assigned lane and returns `approve`, `request_changes`, or `blocked`.
- Includes a PR/MR status mirror line when the project uses structured review lanes.
- Blocks when required review context is missing from the repo, issue, or PR/MR instead of relying on hidden notes.

### Git provider / code review surface

- Stores branch, diff, review comments, CI output, and evidence attachments.
- Acts as the durable handoff unit for code work.
- Shows current review-lane state when Paperclip or another agent board owns detailed review issues.

## Lifecycle

- `backlog`: candidate work, not active.
- `todo`: activation handoff; assigned agent should start.
- `in_progress`: agent is actively working.
- `in_review`: implementation or answer is ready for human/reviewer inspection.
- `done`: accepted with evidence recorded.
- `blocked`: waiting on missing context, access, repo wiring, approval, or dependency.
- `cancelled`: intentionally stopped.

## Evidence standard

An agent claim is not enough.

Good evidence includes:

- exact commands and pass/fail output
- test/build/lint results
- API responses or read-only data checks
- screenshots or traces for UI behavior
- branch and PR/MR links
- fixture data summary
- mutation boundary
- known limitations and follow-up issues
- changelog or decision-log update for multi-issue work

## Default rule

Agents should return receipts, not just results.
