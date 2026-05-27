# Paperclip MR Review Matrix

Use this as a comment on the parent Paperclip issue when a PR/MR enters structured multi-agent review.

```markdown
## MR Review Matrix

Parent issue: <ISSUE-ID>
PR/MR: <URL>
Reviewed SHA: <sha>
Implementation owner: <agent/user>
Orchestrator: <agent/user>

Orchestrator independence:
- [ ] Orchestrator is not the implementation owner.

Required lanes:
- [ ] Correctness - child issue: <ISSUE-ID or pending>
- [ ] Operational Risk - child issue: <ISSUE-ID or pending>
- [ ] UI Behavior - child issue: <ISSUE-ID or waived with reason>
- [ ] Docs / Operator Context - child issue: <ISSUE-ID or waived with reason>

Waived lanes:
- <lane>: <reason, or none>

Child issue states:
- Correctness: <backlog|todo|in_progress|in_review|done|blocked|waived>
- Operational Risk: <backlog|todo|in_progress|in_review|done|blocked|waived>
- UI Behavior: <backlog|todo|in_progress|in_review|done|blocked|waived>
- Docs / Operator Context: <backlog|todo|in_progress|in_review|done|blocked|waived>

Reviewer decisions:
- Correctness: <pending|approve|request_changes|blocked> - <link to comment/evidence>
- Operational Risk: <pending|approve|request_changes|blocked> - <link to comment/evidence>
- UI Behavior: <pending|approve|request_changes|blocked|waived> - <link or reason>
- Docs / Operator Context: <pending|approve|request_changes|blocked|waived> - <link or reason>

Current blocker:
- <none, or issue/comment/link>

Conflicts:
- <none, or summary of conflicting decisions>

Iteration state:
- Current head SHA:
- Last reviewed SHA per lane:
- Lanes reset after latest push:

Final approval packet:
- Status: not ready | ready for human decision | blocked
- Recommendation:
- Human decision requested:
```

## Notes

- Update the matrix after each lane decision.
- Update the matrix after each new commit to the PR/MR branch.
- Do not mark the parent issue done based only on the existence of a PR/MR.
- Keep evidence links re-openable by the human reviewer.

