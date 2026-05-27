# Paperclip MR Review Matrix

> **Status: experimental — preview.** See `paperclip-mr-review-regimen.md` for the full pattern.

Use this as a comment on the parent Paperclip issue when a PR/MR enters structured multi-agent review.

```markdown
## MR Review Matrix

Parent issue: <ISSUE-ID>
PR/MR: <URL>
Reviewed SHA: <sha>
Implementation owner: <agent/user>
Orchestrator: <agent/user, must be different from implementation owner>
Budget: <N reviewers, up to M iterations>
PR/MR status mirror: <comment URL or "not posted yet">

Orchestrator independence:
- [ ] Orchestrator is not the implementation owner of this PR/MR.

Reviewer independence:
- [ ] No review-lane reviewer is the implementation owner of this PR/MR.

Required lanes:
- [ ] Correctness — child issue: <ISSUE-ID or pending>
- [ ] Operational Risk — child issue: <ISSUE-ID or pending>
- [ ] UI Behavior — child issue: <ISSUE-ID or waived with reason>
- [ ] Docs / Operator Context — child issue: <ISSUE-ID or waived with reason>

Waived lanes:
- <lane>: <reason, or none>

Child issue states:
- Correctness: <backlog|todo|in_progress|in_review|done|blocked|waived> | reviewer: <persona>
- Operational Risk: <state> | reviewer: <persona>
- UI Behavior: <state> | reviewer: <persona>
- Docs / Operator Context: <state> | reviewer: <persona>

Reviewer decisions (against reviewed SHA above):
- Correctness: <pending|approve|request_changes|blocked> — <link to comment/evidence>
- Operational Risk: <pending|approve|request_changes|blocked> — <link to comment/evidence>
- UI Behavior: <pending|approve|request_changes|blocked|waived> — <link or reason>
- Docs / Operator Context: <pending|approve|request_changes|blocked|waived> — <link or reason>

PR/MR mirror state:
- Correctness: <not mirrored|mirrored at URL|N/A>
- Operational Risk: <not mirrored|mirrored at URL|N/A>
- UI Behavior: <not mirrored|mirrored at URL|N/A>
- Docs / Operator Context: <not mirrored|mirrored at URL|N/A>

Current blocker:
- <none, or issue/comment/link>

Conflicts:
- <none, or summary of conflicting decisions, verbatim from each>

Iteration state:
- Current head SHA:
- Last reviewed SHA per lane:
  - Correctness: <sha>
  - Operational: <sha>
  - UI: <sha>
  - Docs: <sha>
- Lanes reset after latest push: <list lanes or "none">
- Iteration round: <1 of N>

Budget extension requests:
- <none, or which reviewer asked, what they asked for, decision>

Emergency bypass:
- Status: none | requested | approved | rejected
- Approver:
- Incomplete/stale lanes:
- Risk owner:
- Follow-up issues:

Final approval packet:
- Status: not ready | ready for human decision | blocked
- Recommendation:
- Human decision requested:
```

## Notes

- Update the matrix after each lane decision.
- Update the PR/MR status mirror after each lane decision, block, waiver, reset, or final approval packet.
- Update the matrix after each new commit to the PR/MR branch — record new head SHA, list lanes reset, and bump iteration round.
- Do not mark the parent issue done based only on the existence of a PR/MR or on the matrix saying "ready" — that is the human's call.
- Do not treat a lane as fully handed off until both the parent matrix and PR/MR status mirror show the current lane state.
- Keep evidence links re-openable by the human reviewer. Links to artifacts on a project VM or CI artifact store are fine; raw artifacts committed to the product repo are usually wrong (see "Evidence Storage" in the regimen doc).
- When a lane is `waived`, the waiver text must include the reason. "Not applicable" alone is not sufficient.
- When an emergency bypass is approved, keep the matrix `blocked` or `not ready` until the bypass record includes approver, risk owner, rollback/mitigation plan, and follow-up issues.
