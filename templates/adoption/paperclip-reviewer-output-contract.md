# Paperclip Reviewer Output Contract

> **Status: experimental — preview.** See `paperclip-mr-review-regimen.md` for the full pattern.

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

At least one of the items below must be present and independently re-openable. If none are, this output is treated as `request_changes` regardless of the stated decision.

Tool-call IDs or agent-run transcripts:
- <ids or paths, or N/A>

Commands:
- <command> -> <result and output path>

CI jobs:
- <URL or N/A>

Browser/UI artifacts:
- <screenshot/trace/recording path or N/A — link, do not commit to product repo>

API/log artifacts:
- <URL/path or N/A>

File citations:
- <path:line or path:line-line>

Paperclip/tool traces:
- <comment ID, run ID, tool transcript path, or N/A>

## Findings

- <finding with citation to a specific evidence item above>

## Residual Risk

- <what was not verified and why>

## Recommended Next Action

- <merge/accept with notes | fix and re-review | block on issue | escalate to human>

## Budget Note (if applicable)

- <whether the budget cap was hit; whether an extension was requested; what the orchestrator decided>
```

## Decision Semantics

- `approve`: this lane is satisfied for the reviewed SHA. The reviewer believes a human merging at this SHA would not regret it on this lane's axis.
- `request_changes`: the implementation owner should change something; this lane should re-review the affected scope on the new SHA.
- `blocked`: the reviewer cannot reach a reliable decision because required context, access, environment, tooling, or policy is missing. The orchestrator routes the lane to a different reviewer or escalates.

## Enforcement Rules

- Reviewer output without at least one re-openable evidence artifact should be treated as `request_changes`, even if the stated decision says `approve`.
- An `approve` decision on a lane whose runtime was missing required tooling (e.g., UI lane without browser automation) is invalid and should be re-classified as `blocked`.
- Re-reviews after a pushed fix must produce **new** evidence — new tool-call IDs, new CI URL, new screenshot path with the new SHA in the path. Copying prior evidence forward is not allowed.
- The reviewer does not aggregate cross-lane signals. "Correctness approved already, so I'll approve UI" is not allowed. Each lane decides on its own evidence.
