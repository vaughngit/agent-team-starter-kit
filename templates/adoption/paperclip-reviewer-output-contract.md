# Paperclip Reviewer Output Contract

Use this as the final comment on a review-lane child issue.

```markdown
## Review Decision

Lane: correctness | operational | ui | docs
Parent issue: <ISSUE-ID>
Child issue: <ISSUE-ID>
PR/MR: <URL>
Reviewed SHA: <sha>
Decision: approve | request_changes | blocked
Confidence: low | medium | high

## Scope Inspected

- <files, endpoints, flows, configs, docs, CI jobs, or UI states inspected>

## Evidence

Commands:
- <command> -> <result and output path>

CI jobs:
- <URL or N/A>

Browser/UI artifacts:
- <screenshot/trace/recording path or N/A>

API/log artifacts:
- <URL/path or N/A>

File citations:
- <path:line or path:line-line>

Paperclip/tool traces:
- <comment ID, run ID, tool transcript path, or N/A>

## Findings

- <finding with evidence citation>

## Residual Risk

- <what was not verified and why>

## Recommended Next Action

- <merge/accept with notes | fix and re-review | block on issue | escalate to human>
```

## Decision Semantics

- `approve`: this lane is satisfied for the reviewed SHA.
- `request_changes`: implementation owner should change something and this lane should re-review affected scope.
- `blocked`: reviewer cannot reach a reliable decision because required context, access, environment, or policy is missing.

Reviewer output without at least one re-openable evidence artifact should be treated as `request_changes`, even if the stated decision says `approve`.

