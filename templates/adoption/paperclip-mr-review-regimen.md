# Paperclip MR Review Regimen

> **Status: experimental — preview, not v1.**
> This template is being piloted in a real Paperclip project. The pilot retrospective has not yet been written; the templates here may change after pilot completion. Treat this as a working draft to learn from, not a stable contract. When the pilot retrospective exists, this file should reference it as the canonical adoption example.

**Maintainer note (preview-status content).** When revising these templates, keep the live-agent setup, PR/MR status mirror, reviewer independence, runtime context boundary, and webhook-vs-polling automation model explicit. Editing this regimen does not update live Paperclip agents — see `paperclip-review-agent-setup.md` for the live-agent install step that any adoption must perform separately. Phase 2 webhook automation is specified in `paperclip-git-provider-webhook-plugin-plan.md`.

Use this template when a Paperclip-backed project wants PR/MR review to trigger structured multi-agent review instead of treating "PR opened" or "MR opened" as completion.

This is a coordination pattern. It does not replace project-specific delivery rules, human approval, CI, code review, or deployment checks.

## When To Use This Pattern

Use it when the cost of merging the wrong thing is high:
- Production deployment on merge.
- Real customer data, financial data, PII, or regulated workflows.
- Multi-agent teams where individual reviewers can plausibly approve without verifying.
- Anywhere `in_review` currently means "an MR exists" rather than "decisions have been made on real artifacts."

Skip it when the change is trivial (typo, comment-only, vendored update with no behavior change) or when a single human reviewer is already faster and more reliable than orchestrating multiple agents.

## Core Rule

`in_review` means:

1. An implementation artifact exists, usually a PR or MR.
2. Required review lanes are created as child Paperclip issues.
3. Reviewer agents return evidence-backed decisions.
4. The orchestrator aggregates results into a human-readable approval packet.
5. A human or designated owner makes the final merge, deploy, or acceptance decision.

Opening a PR/MR starts review. It is not completion.

## Design Principles

1. **Few lanes, not many.** Use the smallest set of reviewer lanes that covers the risk.
2. **Evidence must be verifiable.** A reviewer decision needs re-openable receipts: CI URLs, command output, screenshots/traces, logs, file citations, API responses, or Paperclip comments.
3. **Reviewers may not waive their own lane.** Waivers belong to the orchestrator or human owner and must include a reason.
4. **Approval is tied to a reviewed SHA.** A review approves a specific commit or head SHA, not the branch forever.
5. **New commits reset affected lanes.** Re-review lanes whose scope overlaps the new diff.
6. **Disagreement escalates.** The orchestrator does not vote away a blocking reviewer finding.
7. **Reviewer budget is explicit.** Name expected lanes and iteration limits before starting.
8. **Auto-merge stays off by default.** The regimen makes human approval easier; it does not silently merge production-impacting work.
9. **Orchestrator independence.** The orchestrator must not be the implementation owner of the PR/MR under review. The implementation owner responds to findings; the orchestrator creates and routes review lanes and aggregates decisions.
10. **Role definitions must be explicit, not inferable.** "The CTO orchestrates" is ambiguous when the CTO is also the implementation owner. Spell out who orchestrates each pilot, not just by title — by their relationship to the PR/MR under review. This rule exists because role-definition ambiguity survived multiple review passes in the original pilot and was only caught when a human questioned the assignment.
11. **Reviewer independence.** The implementation owner must not serve as a review-lane reviewer for their own PR/MR. If a project lacks enough reviewer personas to honor this, the orchestrator records the lane as blocked or routes to a human reviewer rather than letting the owner approve their own work.

## The Review System

This regimen is a system, not a checklist. Four axes define it: the **lifecycle** every PR/MR moves through, the **materialization** that gives each state a canonical artifact, the **authority** model that says who may act on what, and the documented **behavior under change** that keeps the system coherent when state mutates mid-review.

This section is scaffolding. It names the axes, points at the existing sections that fill them in, and marks the cells the adopting project's pilot retrospective is expected to close. The mechanics that follow (lanes, output contract, iteration rules, phases) fill in cells that are already settled by the regimen; cells marked TBD here are deliberately left to adopters because they are project-specific or because the founding pilot has not yet generated evidence to close them.

### Axis 1: Lifecycle

Every PR/MR moves through a fixed sequence of states. Each transition has a trigger, an actor, and an exit condition. A change is in exactly one state at a time.

| State | Trigger to enter | Actor responsible | Exit condition |
|---|---|---|---|
| `open` | Implementation owner pushes the PR/MR and links it to the parent issue | Implementation owner | PR/MR exists with description and linked parent issue; parent moves to `in_review` |
| `lanes-enumerated` | Parent enters `in_review`. Phase 1: orchestrator decides lanes manually, optionally considering implementation-owner suggestions. Phase 2: plugin classifies from diff. | Orchestrator | Required lanes named, child issues created in `backlog`, matrix posted on parent, initial PR/MR status mirror posted |
| `under-review` | Orchestrator activates lanes one at a time (`backlog -> todo`) per the activation rule | Orchestrator | All required lanes have posted a decision against the current head SHA |
| `aggregated` | Last required lane returns a decision | Orchestrator | Parent matrix refreshed; PR/MR status mirror updated; conflicts (if any) quoted in matrix `Conflicts` section; any `request_changes` lane routed back to implementation; any `blocked` lane escalated |
| `decision-ready` | Aggregation complete with all required lanes approved or waived, no unresolved conflicts, no unresolved `request_changes`, and no blocked lanes | Orchestrator | Approval packet ready for human, mirrored on PR/MR |
| `merged-or-reverted` | Human merges, reverts, or pushes back to `under-review` | Human owner performs the git-provider action; orchestrator performs Paperclip close-out within minutes of merge/revert notification | PR/MR closed; parent moves to `done` with close-out comment, or back to `in_progress`/`under-review` if merge is rejected or reverted |

Cells TBD by your project's pilot:
- Whether each lifecycle state needs a Paperclip-status counterpart on the parent issue, or whether it remains implicit in `in_review` plus matrix-comment content.
- Whether `aggregated` and `decision-ready` are actually distinguishable in your project's workflow, or collapse into one state.

### Axis 2: Materialization

For every fact the system holds, exactly one location is the source of truth. Everything else is a derived view or a pointer. Two systems of record for the same fact is a regimen bug — your pilot should expose any cases where it happens.

| Fact | Source of truth | Derived views |
|---|---|---|
| Required lanes for this PR/MR | Parent matrix comment | Child issue titles |
| Per-lane decision | Child issue status + decision comment | Parent matrix entry |
| Git-provider-visible lane status | Parent matrix comment + child issue states | Mandatory PR/MR status mirror comment, updated when lanes are enumerated and when any lane resolves, blocks, or is waived |
| Reviewed SHA | Reviewed-SHA field in the decision comment | Matrix entry |
| Evidence for a decision | Path or URL cited in the decision comment | None — evidence is not duplicated; only pointed to |
| Waivers (which lane, by whom, why) | Inline annotation on the lane entry in the matrix comment | None |
| Conflicts between lanes | Matrix comment `Conflicts` section, quoting both decisions verbatim | None |
| Authority transfer (orchestrator change, redirect) | Comment on the affected issue | Project's own pilot tracking file, if one exists |
| Final approval packet | Parent matrix comment `Final approval packet` section | Short mirror comment on the PR/MR linking back to the parent |
| Post-merge close-out | Parent issue close-out comment with merge SHA, deploy info if applicable, and final approval packet link | Git-provider PR/MR merge/deploy metadata |
| Pilot findings during your Phase 1 | Project-local pilot tracking file (outside the live Paperclip issue) | None |
| Post-pilot lessons | Project's own retrospective document | None |

Cells TBD by your project's pilot:
- Where ratification records live when a new orchestrator inherits prior decisions from a non-independent orchestrator (open question the founding pilot surfaced; resolution may vary by project).
- Whether cross-PR/MR evidence reuse ever happens, and if so the discovery path.
- Whether the matrix `Conflicts` section is the right home when a conflict is only partially resolved.

### Axis 3: Authority

Each role has named powers and named limits. The limits matter as much as the powers — they are the seams the regimen relies on for independence.

| Role | Authorities | Limits |
|---|---|---|
| Implementation owner | Opens the PR/MR; pushes fixes in response to findings; signals affected lanes are ready for re-review after pushing a fix; escalates to human after two iteration rounds with unresolved `request_changes` (per Iteration Rule) | May not orchestrate; may not approve its own lanes; may not waive lanes |
| Orchestrator | Enumerates required lanes; routes them one at a time; refreshes the matrix; maintains the PR/MR status mirror; surfaces conflicts to human; manages reviewer budget; calls budget-extension decisions; closes out the parent issue after merge by setting it to `done` and posting a close-out comment with merge SHA, deploy info if applicable, and final approval packet link | May not be the implementation owner (Principle 9); may not aggregate or vote across lane decisions (Principle 6); may not waive lanes without a written rule (Principle 3) |
| Reviewer (per lane) | Posts a decision; cites evidence; flags residual risk; requests budget extension; includes the PR/MR status mirror line per the Reviewer Output Contract | May not waive itself (Principle 3); may not approve without verifiable evidence (Principle 2); may not carry forward evidence across SHAs (Principle 4); may not serve as reviewer for its own implementation work (Principle 11) |
| Human owner | Final merge, revert, or accept; resolves conflicts; grants budget extensions; may override any decision with a recorded reason | Should not be required for routine state transitions — that is orchestrator-flavored work; overriding without a recorded reason undermines the audit trail |

Cells TBD by your project's pilot:
- May the orchestrator reject a lane request from the implementation owner? Under what rule?
- May a reviewer recuse itself and request re-assignment?
- May the implementation owner dispute a finding — and if so, does that mutate the reviewer's posted decision or surface as a separate comment for the orchestrator to route?
- When the orchestrator invokes the human, is that a comment, a `currentParticipant` mutation, an out-of-band ping, or some combination?
- Does the human's "override with recorded reason" power also apply mid-review (e.g., forcing a lane closed), or only at the final merge gate?

### Axis 4: Behavior under change

State changes mid-review are not edge cases — they are the normal mode for any non-trivial PR/MR. The system needs documented behavior for each kind of change, not improvisation.

| Change | Documented behavior | Reference |
|---|---|---|
| Implementation owner pushes a new SHA | Affected child issues reset; unaffected children retain prior approval with prior evidence cited | Iteration Rule; Lane Reset Classifier |
| Two lanes return conflicting decisions | Orchestrator quotes both in matrix `Conflicts` section, routes parent to human; does not aggregate | Conflict Resolution; Principle 6 |
| Reviewer wants to exceed budget | Reviewer posts budget-extension request comment on its child issue; orchestrator or human decides | Reviewer Budget |
| Orchestrator changes mid-review (e.g., redirect for independence) | TBD — open question: re-review all prior decisions, ratify if reviewer independence held, or case-by-case with recorded reason. Founding pilot left this for the adopting project to decide. | Open |
| Spec or contract changes mid-review (new principle or new mandatory rule added) | TBD — same shape as the orchestrator-change row above. A mid-pilot mutation creates a pre/post split among lane decisions: ones decided under the old contract vs. ones bound by the new. Pick a rule in your retrospective. | Open |
| Human merges the PR/MR after final approval packet | Human merge is the git-provider action; Phase 1 human pings orchestrator with "PR/MR merged, please close out"; orchestrator marks parent issue `done` and posts close-out comment with merge commit, deploy info if applicable, and approval packet link | Post-merge close-out rule |
| Implementation owner goes silent or abandons | TBD | Open |
| Reviewer goes silent or abandons | TBD | Open |
| PR/MR is rebased or squashed (SHAs rewritten) | TBD | Open |
| Required lane is added or removed after enumeration | TBD | Open |
| PR/MR is closed without merge while review is in flight | TBD | Open |
| Parent's `executionState` or `executionPolicy` is `null` (no policy attached) | Phase 1: fall back to comment tagging the reviewer rather than mutating `currentParticipant`. Phase 2 behavior is TBD per adopter — the founding pilot only validated the Phase 1 fallback. | Known Gotchas |
| Reviewer posts a decision but uses Markdown emphasis (`Decision: **approve**`) or other strict-format variants | Phase 2 plugin parses tolerant variants and scans for recoverable approvals during reconciliation; rigid parsing leaves approved lanes blocked. | Phase 2 |
| PR/MR description references multiple Paperclip issues (e.g. `closes #42, supersedes #17`) | Phase 2 plugin resolves the parent by documented precedence (explicit trailer first, source-branch or title second, description fallback third) and blocks on ambiguous matches rather than guessing. | Phase 2 |

Cells TBD by your project's pilot:
- Most rows above. The pilot is expected to bite on a subset; the retrospective closes those rows first and explicitly defers the rest.

---

Each axis above is a contract the regimen owes its users. The sections that follow (Default Lanes, How This Maps Onto Paperclip, Lifecycle, Phased Rollout, Orchestrator Role, Implementation Owner Role, Child Review Issue Rule, Iteration Rule, Lane Reset Classifier, Conflict Resolution, Reviewer Budget, Evidence Storage, Emergency Bypass, Completion Rule, Known Gotchas) fill in the mechanics for the cells that are already decided. Your own pilot retrospective is the venue for closing the TBD cells — and for naming any new cells the pilot surfaces that this scaffolding did not anticipate.

## Default Lanes

| Lane | Trigger | Reviewer focus |
|---|---|---|
| Correctness | Any code change unless the PR/MR is docs-only. | Behavior, scope, tests, data correctness, permissions, migration safety, mutation boundaries. |
| Operational Risk | CI, deployment, infra, environment, dependency, or config changes. | Pipeline behavior, release/deploy risk, rollback path, smoke checks, runner/environment assumptions. |
| UI Behavior | User-visible UI, interaction, accessibility-relevant markup, or visual state changes. | Real browser interaction, screenshots/traces, responsive behavior, adjacent-flow regressions. |
| Docs / Operator Context | Operator-facing behavior, runbooks, setup, policy, or activation semantics change. | Accuracy, downstream activation notes, docs match what changed. |

Do not create all lanes by default. Create only the lanes required for the current PR/MR. Record waived lanes and reasons in the review matrix.

## How This Maps Onto Paperclip

The regimen uses Paperclip's existing primitives rather than inventing new ones. Adopters should understand this mapping before implementing.

| Regimen concept | Paperclip primitive |
|---|---|
| Parent implementation issue | The existing Paperclip issue for the change. Moves to `in_review` once the PR/MR opens. |
| Per-lane reviewer | A child Paperclip issue linked to the parent. Paperclip issue IDs are sequential numerics assigned at creation; record the real IDs in the parent matrix. Title format: `[Review][<parent-id>][<Lane>] <PR/MR title>`. |
| "Who must decide on this lane now" | The child issue's `executionState.currentParticipant` (when an execution policy is attached) or the child issue's assignee (when no policy is in use). See "Known gotchas" below for the policy-null case. |
| Lane decision | The child issue moves to `done` (approve), to `blocked` (cannot decide), or stays in `in_review` with a `request_changes` comment. |
| Review matrix | A comment on the **parent** issue listing child IDs and current state. **This is text, not a structured object Paperclip enforces.** In Phase 1 the orchestrator updates it; in Phase 2 a plugin refreshes it from child issue states. |
| Git-provider review visibility | A required PR/MR status mirror comment. It is derived from Paperclip state, but it must be visible on the PR/MR because that is where the human merge decision happens. |
| Evidence | Comments on each child issue, plus links to artifacts stored outside the product repo. See "Evidence storage" below. |
| Merge-readiness check | Phase 1: a human reads the parent matrix. Phase 2: a plugin computes readiness from child issue states and refuses to advance the parent until all required children are `done`. |
| Reviewer-agent behavior | Live Paperclip agent configuration or capability text. Use `paperclip-review-agent-setup.md`; repo template changes alone do not update live agents. |

## What Paperclip Does NOT Give You

These are gaps adopters will hit if they assume Paperclip has them. Plan around them.

- **No native PR/MR-opened webhook into Paperclip.** Phase 1: the implementation owner or orchestrator creates the child review issues manually when the PR/MR opens. Phase 2: build a Paperclip plugin that declares an inbound webhook for git-provider MR events.
- **No physical merge block on the git provider.** Paperclip cannot prevent a merge on GitLab / GitHub / etc. It can only refuse to advance its own state. The actual safeguard against premature merge stays with the human merge step or with git-provider-side branch protection.
- **No automatic PR/MR status mirror unless you build it.** Paperclip comments and child issue state are not automatically visible on GitHub/GitLab. Phase 1: the orchestrator posts and maintains a PR/MR status mirror manually. Phase 2: the plugin updates it idempotently when child issue state changes.
- **No automatic live-agent update from repo templates.** Editing this regimen does not change the agents Paperclip will wake. Update reviewer-agent capability/instruction text separately and verify it in Paperclip.
- **No native "lane reset on push."** Phase 1: the implementation owner re-opens or re-flags affected child issues when pushing a fix. Phase 2: a plugin watches `issue.updated` plus PR/MR head-SHA changes and resets affected children.
- **No outbound webhooks.** Paperclip's plugin system is in-process. Plugins are the integration path, not a separate webhook receiver.
- **No native evidence storage.** Issue comments are durable; binary artifacts (screenshots, traces, recordings) live elsewhere and are linked from comments.
- **`executionState` and `executionPolicy` may be `null` on issues that were never routed through a policy.** See "Known gotchas."

## Lifecycle

```text
implementation issue in_progress
-> PR/MR opened
-> parent issue in_review
-> orchestrator creates child review issues in backlog
-> orchestrator activates lanes intentionally (backlog -> todo, one at a time)
-> reviewers post evidence-backed decisions on their child issues
-> orchestrator updates the review matrix on the parent
-> human receives approval packet
-> merge/deploy/acceptance only after explicit human approval
```

## Phased Rollout

Do not try to build all of this at once. The phases below let adopters validate the pattern manually before paying the engineering cost of automation.

### Phase 1: Manual, Single-PR/MR Pilot

- Pick one PR/MR in your project that exercises multiple lanes (so the pilot tests the regimen, not just one lane).
- The orchestrator manually creates child review issues per `paperclip-review-child-issue.md`.
- Children start in `backlog`. The orchestrator promotes them to `todo` one at a time per the activation rule.
- Reviewers post decision comments per `paperclip-reviewer-output-contract.md`.
- The orchestrator maintains the review matrix on the parent per `paperclip-mr-review-matrix.md`.
- The orchestrator walks one iteration round if findings warrant.
- Append a retrospective to your project's adoption notes before generalizing.

**Do not skip the pilot.** Project-specific gotchas (execution policy state, adapter capabilities, reviewer tool availability) will surface in the first pilot and may require template changes before broader rollout.

### Phase 2: Paperclip Plugin

Build only after Phase 1 has produced a retrospective and the manual orchestration has been observed working end-to-end.

Use `paperclip-git-provider-webhook-plugin-plan.md` for the implementation contract. The default model is: git-provider PR/MR webhook as the primary signal, scheduled reconciliation polling as a backup, and Paperclip heartbeat only as an agent execution primitive — not as the external-state detector.

The plugin subscribes to Paperclip's in-process event bus (`server/src/services/plugin-event-bus.ts`):

- Subscribes to `issue.updated` on parent issues. When a parent flips to `in_review` and has a linked PR/MR URL, the plugin classifies changed files, creates child review issues, and writes the review matrix comment.
- Subscribes to `issue.updated` on child issues to recompute parent readiness and refresh the matrix as decisions land.
- Declares an inbound webhook in the manifest for PR/MR events (see Phase 3).

The plugin must be idempotent. Compose the idempotency key from the git provider delivery/event ID, project ID, PR/MR URL or number, the **event action** (`open`, `update`, `merge`, `close`, etc.), and head SHA. The action component is load-bearing — without it, two different events on the same SHA (e.g., a `comment_added` followed by a `merge`) collide and only the first is processed. A retried webhook or repeated `issue.updated` event must update the same parent matrix and child issues, not create duplicate review lanes.

Effort estimate: comparable to any Paperclip plugin that uses the same SDK surface (`ctx.events.subscribe`, `ctx.api.issues.*`, manifest webhooks). If your project will also build other Paperclip plugins (e.g., a Linear bridge), ship one first to pay the SDK learning tax once.

#### Parent-issue resolution precedence

PR/MR descriptions reference issues incidentally as well as canonically — `Closes #42, supersedes #17, see also #9` is normal. Guessing the parent from any reference produces wrong-parent mutations on real-world MRs. Resolve in this order, and **block on ambiguous matches rather than guess**:

1. **Explicit marker (load-bearing).** The PR/MR description includes an explicit `Paperclip-Parent: <id>` trailer (or whatever format your project commits to). Use it without further parsing.
2. **Source-branch or title match.** The source branch encodes the parent issue ID (e.g., `fix/CLE-42-...`), or the PR/MR title leads with the parent ID.
3. **Description body fallback.** Scan the description for the **first** issue reference in `Closes` / `Fixes` / `Resolves` syntax. Ignore mentions in `see also`, `related to`, or buried inside a longer reference list.
4. **Ambiguity blocks.** If two precedence levels resolve to different parents, or if multiple closing references exist at the same level, the plugin marks the inbound delivery `parent-ambiguous` and posts a blocking comment on the PR/MR rather than picking one.

Adopters: pick the explicit trailer format **before** Phase 2 plugin work begins, and add it to your PR/MR template. Implementation owners should ship a parent trailer on every PR/MR; the precedence above is the safety net for missing or malformed ones.

#### Plugin tolerance for reviewer output variants

Live reviewer agents drift from any strict output format over time — Markdown emphasis (`Decision: **approve**`), trailing punctuation, alternate casing, decision lines appearing inside summary tables instead of as bare lines. Rigid parsing turns drift into "blocked" lanes that an adopter then has to recover manually.

The Phase 2 plugin must:

- Parse reviewer-disposition lines tolerantly — strip Markdown emphasis, normalize casing, accept the documented decision values (`approve`, `request_changes`, `blocked`, `waived`) in any of those formats.
- On reconciliation, scan child issue comments for a recoverable approval before deciding the lane is blocked. A reviewer that posted a valid approval comment but didn't transition the child issue is recoverable, not lost.
- Log the parser's interpretation back to the child issue so reviewers see what the plugin understood. This catches drift early instead of at the next reconciliation pass.

The strict `paperclip-reviewer-output-contract.md` format remains the spec for what reviewers should produce; the plugin's tolerance is the safety net so drift doesn't silently orphan approved lanes.

#### Lane activation lands in `todo`, not `backlog`

Phase 1's "create lanes in `backlog`, promote one at a time" exists so a human can observe each reviewer wake and intervene before budget is burned. **Phase 2 plugins explicitly override that default** — when the plugin enumerates required lanes, it creates the child issues directly in `todo`. Otherwise the plugin's work silently stalls in `backlog`, no reviewer wakes, and adopters chase "why are no reviewers running" until they find the Phase 1 hand-step is the gap. Document this override in the plugin manifest and in adoption notes so a later reader doesn't reintroduce the `backlog` step thinking they are "following the regimen."

#### SHA-scoped lane reset on new commits

Phase 1 resets affected children in place. **Phase 2 plugins should supersede instead of mutate**: on a new SHA, create *new* SHA-scoped child issues directly in `todo`, and leave prior children intact as historical evidence with their reviewed-SHA recorded. Suggested title format: append the short SHA (e.g., `[Review][<parent>][<Lane>] <title> @ <sha7>`).

Rationale: in-place mutation loses the per-SHA decision history. With supersede, the parent matrix becomes a per-SHA timeline — reviewers re-pulling context see what was approved at which SHA without git archaeology.

#### Scheduled reconciliation as a safety net

Webhooks miss events — provider outages, plugin restarts mid-delivery, dropped ingress hops, retries the provider gives up on. None are theoretical; all surface during real activation. The plugin must run a **scheduled reconciliation pass** that converges drift:

- Pull the open PR/MR list from the git provider; ensure the parent Paperclip issue exists in the expected state and that required child lanes are present and current against the head SHA.
- Re-attempt previously-blocked parent-ambiguous deliveries.
- Re-scan child issues for recoverable approvals (per "Plugin tolerance for reviewer output variants") and converge their state.
- Reconcile merge close-out for PR/MRs that closed while the plugin was unavailable.

Cadence: every 15 minutes during the activation pilot (webhook gaps are most likely then), tunable to 30–60 minutes once stable. Reconciliation must be idempotent — re-running on a converged system makes no mutations.

#### Phase 2 plugin acceptance tests

Exercise these cases against fixture payloads or a sandboxed Paperclip instance before declaring the plugin ready. Minimum bar — add project-specific cases on top:

- **Valid merge happy path** → parent closes out with merge SHA, deploy info if applicable, and approval-packet link.
- **Duplicate merge delivery** → second delivery is a no-op; close-out comment is not duplicated.
- **Invalid auth** → request fails closed; no Paperclip mutation occurs.
- **Ambiguous parent** → delivery marked `parent-ambiguous`, blocking comment posted on the PR/MR, no parent mutation.
- **Missed-webhook reconciliation** → plugin is down when a merge event fires → scheduled pass converges the parent to `done` after restart, even with no webhook delivered.
- **Tolerant parse variants** → `Decision: **approve**` (Markdown emphasis) or decision line inside a summary table → lane converges to `done`.
- **SHA reset** → new SHA arrives on the same PR/MR → fresh SHA-scoped child issues created in `todo`; prior children remain intact.
- **Idempotent pending-status retry** → repeated `pending` status that the provider already accepted → no-op, not a failure.

### Phase 3: Inbound Git-Provider Webhook

> **Prerequisite — automatic close-out requires public ingress AND a registered webhook.** Automatic merge close-out does not work until BOTH of these are live:
> 1. **A public ingress endpoint for your Paperclip instance.** The plugin's inbound webhook must be reachable from your git provider. This is infrastructure you provision (a tunnel, reverse proxy, or ingress with a public hostname) — the plugin does not establish it. **Installing the plugin is necessary but NOT sufficient; installed does not mean reachable.**
> 2. **A registered git-provider webhook** pointed at the plugin's webhook path with a shared secret (see "Registering the Git-Provider Webhook" below).
>
> Until both are live, close-out is **manual** — the orchestrator closes the parent issue after merge, exactly as in Phase 1. Treat the manual path as the supported fallback, not a failure state. Do not assume a merge auto-closes the Paperclip parent until you have tested the full delivery chain end to end with a real merge.

This is the plugin's inbound webhook, not a separate receiver. Mount it at `/api/plugins/<your-plugin-id>/webhooks/<provider>` and expose it publicly via whatever tunnel/ingress your Paperclip instance uses for plugin webhooks. The full delivery chain that has to be live is:

```text
git-provider MR/PR merge event
  -> your public ingress hostname
  -> tunnel / reverse proxy (path-only)
  -> Paperclip plugin webhook endpoint (/api/plugins/<plugin-id>/webhooks/<provider>)
  -> Paperclip parent-issue close-out
```

Every hop in that chain must exist and be reachable. The plugin only owns the last two hops; the ingress hostname and tunnel are infrastructure you stand up separately.

Security floor (non-negotiable for any plugin webhook exposed publicly):

- **Fail-closed signature verification.** Prefer cryptographic HMAC if the git provider supports it. Otherwise, fail closed on the provider's plain shared-secret token. Verification error returns 401, never 200. No unsigned requests, no soft-fail-on-missing-secret paths.
- **Path-only exposure.** The tunnel/ingress should expose only declared plugin webhook paths. The Paperclip UI, core REST API, board claim, and agent dispatch must not be reachable on the same public hostname; everything else returns 404 at the ingress.
- **IP allowlist as defense in depth.** Where your ingress can restrict the webhook path to the git provider's published IP ranges, do so. Signature verification is the primary defense; IP allowlist is secondary.
- **Audit log.** Every inbound webhook delivery (accepted or rejected) lands in Paperclip's `plugin_webhook_deliveries` table. Review for rejected deliveries spiking — that is the signal a secret leaked or rotated incorrectly.
- **Host-layer status-code wrapping caveat.** Some Paperclip host versions wrap plugin auth failures into a generic 5xx (e.g., 502) at the externally-visible response. What matters for security is the plugin's fail-closed *mutation* behavior — verify in `plugin_webhook_deliveries` that no Paperclip writes occurred for a rejected delivery, rather than relying on the status code your tunnel sees. Track host-side status passthrough as a separate issue, not as a plugin bug.

#### Registering the Git-Provider Webhook

Once the public ingress is live, register the webhook on the git provider so merge events reach the plugin. The exact path differs by provider:

- **GitLab:** Project → Settings → Webhooks → add URL `https://<your-ingress-host>/api/plugins/<plugin-id>/webhooks/<provider>`, set the Secret token to the value your plugin's secret reference resolves to, and enable **Merge request events** (plus any other events the plugin handles). GitLab sends the token in the `X-Gitlab-Token` header; the plugin must compare it fail-closed.
- **GitHub:** Repo (or org) → Settings → Webhooks → add the same URL, set **Secret**, choose `application/json`, and subscribe to **Pull requests** (and any others the plugin handles). GitHub signs the body with HMAC-SHA256 in `X-Hub-Signature-256`; prefer verifying that signature over a plain token.

After registering, send a test delivery from the provider's webhook UI and confirm it lands in `plugin_webhook_deliveries` with a 2xx (or your documented fail-closed status for a deliberately bad signature). A webhook that points at the wrong path silently 404s and produces no close-out — verify the path matches the plugin manifest's declared endpoint exactly.

#### Idempotency floor for outbound calls

The plugin's outbound calls to the git provider are not always retry-safe by default. Two failure modes show up during real activation that adopters should handle in the plugin, not at the host:

- **Duplicate status transitions on the same SHA.** Providers return `4xx` on repeated transitions — GitLab returns `400 "Cannot transition status … from :pending"` when the plugin tries to re-set a status that is already in the target state. Treat this specific case as an idempotent no-op, not a failure. A bare `try/catch-and-rethrow` on the status POST turns transient retries into `502`s wrapped by Paperclip's host layer.
- **Match on the structured signal, not on the human-readable error string.** Key the no-op rule on the provider's HTTP status code plus the structured error body. String-matching the provider's English error wording couples the plugin to that wording and breaks on minor localization or release-note changes — exactly when you can't afford a regression.

The plugin must distinguish "already in the target state" (no-op) from "cannot transition for a different reason" (real error) and only swallow the former.

#### Merge timestamp fallback

Merge webhooks do not always include `merged_at`. GitLab specifically omits it on some merge-action deliveries and provides `actioned_at` instead. The plugin's close-out logic must fall back to `actioned_at` (or the provider's equivalent action timestamp) — but **only when the payload's `action` or `state` indicates the MR is actually merged**. Unconditional use of an action timestamp as a merge timestamp mislabels close-out evidence on non-merge events, which also carry `actioned_at`.

Treat `merged_at` as `string | undefined`, declare the fallback field in the same payload type, and gate the fallback on the merge-action condition. Cover both the present and absent cases with tests against fixture payloads captured from real deliveries.

### Phase 4: Merge Readiness Gate

The gate is implemented inside the Phase 2 plugin. It cannot block merge on the git provider, and — important nuance from real pilot operation — **it also cannot enforce parent-issue state transitions inside Paperclip core.** Paperclip exposes no merge-blocking hook on the git provider side and no enforced transition gate on the parent side. What the gate can do is surface readiness so the human merge step is a one-line read instead of a manual audit:

- **Mirror readiness onto the PR/MR status check.** Update the git-provider status (`paperclip/mr-review` or equivalent) from `pending` to `success` only when all required child issues are `done` against the current head SHA. The merge button's annotation then reflects Paperclip state.
- **Post a structured "not ready" comment on the parent issue** listing missing lanes when something attempts to advance the parent prematurely. This is a visible warning, not an enforced block.
- **Surface readiness as a parent-issue field** that the UI can render.

The actual safeguard against premature production merge stays with (a) the human merge step, and (b) any git-provider branch protection or required-status checks the project chooses to enable. The Phase 4 gate does not replace either.

## Orchestrator Role

The orchestrator:

- Confirms the implementation artifact and reviewed SHA.
- Chooses required lanes.
- Creates child review issues in `backlog`.
- Moves one lane to `todo` only when ready to wake that reviewer.
- Maintains the parent review matrix.
- Maintains the PR/MR status mirror so the git-provider review surface shows current lane state.
- Aggregates reviewer decisions.
- Escalates conflicts and blockers.
- Records waivers and reasons.
- Produces the final approval packet.
- After human merge/revert, closes out the parent issue with merge SHA, deploy info if applicable, and approval packet link.

The orchestrator must not be the implementation owner of the PR/MR under review.

## Implementation Owner Role

The implementation owner:

- Opens the PR/MR or equivalent artifact.
- Provides the implementation summary and validation evidence.
- Responds to reviewer findings.
- Pushes fixes when requested.
- Does not create, route, waive, or close review lanes for their own work.

## Reviewer Agent Behavior Contract

This contract describes how a live reviewer agent should *behave* once it wakes — independent of the output format (which is owned by `paperclip-reviewer-output-contract.md`). Adopters install this behavior through the agent's system prompt or capability text. Editing this regimen file does not update live agents; see `paperclip-review-agent-setup.md` for the install step.

- **Trigger.** The reviewer wakes when its child issue moves into its lane (orchestrator-driven in Phase 1, plugin-driven in Phase 2). The reviewer does not self-poll for work and does not act on a child issue still in `backlog`.
- **Discovery.** The reviewer reads (a) the child issue body for the MR URL, diff pointer, originating issue ID, lane scope, and any prior decision, and (b) the MR diff **at the recorded SHA** — not the latest branch tip. Information needed beyond what is in the child issue, parent issue, PR/MR, or repo must be flagged as a context gap, not silently sourced from a private knowledge base.
- **Output — both surfaces, not just one.** The reviewer posts its review **both** as a comment on the MR (so the merge decision happens with the review visible there) **and** as a decision comment on the child issue (so Paperclip state and the matrix can reflect it). Each comment ends in a parseable line of the form `Decision: approve | request_changes | blocked | waived`. Posting to only one surface is a known failure mode — the founding pilot reproduced it and corrected the agents to always post to both.
- **Status.** After posting the decision, the reviewer moves its **child** issue to the corresponding state. The reviewer does not transition the **parent** issue — that is the orchestrator's (Phase 1) or the plugin's (Phase 2) job.
- **Budget.** When scope expansion would exceed the reviewer budget, the reviewer stops and posts a `budget-extension-request` comment on the child issue instead of silently overrunning. The orchestrator or human grants or denies; the reviewer waits for that signal before continuing.
- **No self-re-review on a new SHA.** When a new SHA arrives, the plugin (Phase 2) creates a fresh SHA-scoped child issue; the reviewer waits for that new issue rather than re-evaluating the stale one. Prior child issues remain as historical evidence and are not mutated.

Adopters who update only this regimen file without updating live agent prompts will see agents continue to follow their old behavior. Treat live-agent verification — re-reading the agent configuration from Paperclip after editing — as part of every regimen change that touches reviewer behavior.

## Child Review Issue Rule

Create one child Paperclip issue per required review lane.

Recommended title format:

```text
[Review][<parent-issue-id>][<lane>] <PR/MR title>
```

Create child issues in `backlog`. Move a child issue to `todo` only when the orchestrator intends to activate that reviewer. Do not bulk-activate child issues — activate one at a time so reviewer budget can be observed and adjusted.

## Iteration Rule

When the implementation owner pushes a new commit:

1. Compare the new head SHA to the last reviewed SHA per lane (track this in the matrix).
2. Reset affected lanes whose triggers overlap the new diff back to `in_review` or `todo`, depending on your project's status conventions.
3. Require fresh evidence (new commands, new CI URL, new screenshot path) for affected lanes. They may not copy prior evidence forward.
4. Keep unaffected-lane approvals only if their reviewed SHA and scope remain valid; cite the prior evidence in the matrix rather than re-running.

Done criterion for the implementation owner: every required child issue is `done` against the current head SHA, or an unresolved finding has been surfaced to the human. After two review rounds with unresolved `request_changes`, stop and escalate rather than looping indefinitely.

## Lane Reset Classifier

Use a project-specific classifier to decide which lanes reset after a new push. Start conservative, then tune after the pilot retrospective.

| Changed path or signal | Reset lanes |
|---|---|
| `frontend/**`, `app/**`, UI components, routes, styles, accessibility markup | UI Behavior, Correctness |
| `backend/**`, API handlers, auth, permissions, data mutations, migrations | Correctness, Operational Risk |
| `infra/**`, deployment manifests, CI, dependency locks, env/config, secrets wiring | Operational Risk, Correctness when runtime behavior changes |
| `docs/**`, runbooks, setup, operator-facing behavior descriptions | Docs / Operator Context |
| Test-only changes | Correctness if the tests alter confidence for changed behavior; otherwise record no reset with reason |
| Changelog, issue templates, process docs | Docs / Operator Context, Operational Risk if activation/deployment semantics change |

The classifier is guidance, not an excuse to skip judgment. If a change crosses boundaries, reset every affected lane.

## Conflict Resolution

When two child review issues return conflicting decisions on overlapping scope (e.g., Correctness approves auth changes while Operational flags them as deploy-risky), the orchestrator:

1. **Does not aggregate or vote.** Conflicting reviewer outputs are not majority-decision material.
2. **Quotes both decisions verbatim** in the parent issue's matrix comment under a `Conflicts:` section.
3. **Surfaces the conflict to a human.** Routes via comment or approval request; if the project's execution policy exposes a safe mutation path, sets `currentParticipant` to the human reviewer. Otherwise, tags them.

The human resolves, optionally by asking a third lane to weigh in.

## Reviewer Budget

Each PR/MR gets a default budget:

- Up to 3 child review issues in the first pass.
- Up to 2 additional iterations (re-reviews after pushed fixes).
- Soft cap on tool calls per reviewer; reviewer notes when it bumps the cap in its decision comment.

If a reviewer wants to exceed budget (expand scope, run more checks), it stops and posts a budget-extension request as a comment on its child issue. The orchestrator or human decides whether to extend.

This is not about saving pennies. It is about preventing an agent loop from quietly burning an hour exploring tangents.

## Evidence Storage

Evidence linked from reviewer comments may include artifacts that contain sensitive data (PII, customer data, financial state, security tokens, internal URLs, screenshots of production UI). Default rule: **link, don't commit.**

Recommended storage:
- Artifacts live on your Paperclip host (or a project-controlled VM / object store) under a per-PR/MR/lane/SHA directory structure. Reviewer comments cite the path or URL.
- CI job artifacts (the git provider's native artifact store) are also acceptable storage for evidence that was produced during CI.
- Commit artifacts to the product repo **only** after explicit scrubbing and labeling as sanitized fixtures (e.g., under a `fixtures/` directory with redacted test data).

Retention and access:
- Evidence must remain readable by the human approver for at least the lifetime of the PR/MR plus the project's normal rollback window.
- Access should be no broader than the people and agents allowed to review the PR/MR. Do not store sensitive evidence in a public bucket, public CI artifact, or broadly shared drive.
- Reviewer comments should state the evidence retention location and any access requirement. If evidence must be deleted quickly, record that in the parent matrix and keep a sanitized summary.

When in doubt, link, don't commit. The product repo is not the right home for raw browser captures, transaction logs, or anything that includes production identifiers.

## Emergency Bypass

A human may merge, deploy, or accept a PR/MR before every lane is complete only through an explicit bypass.

Required bypass record on the parent issue:
- Who approved the bypass.
- Which lanes were incomplete, blocked, or stale.
- Why waiting would be riskier than proceeding.
- Risk owner.
- Rollback or mitigation plan.
- Follow-up issue for every skipped lane or unresolved finding.

The orchestrator may prepare the bypass packet, but only a human owner can approve it. Bypass is not a normal success path and should be counted in the retrospective.

## Completion Rule

The parent issue is not ready for final human approval until:

- required lanes are `done` against the current reviewed SHA, or explicitly waived with a reason;
- blockers and conflicts are resolved or escalated;
- the approval packet links to the reviewer decisions and evidence;
- the PR/MR status mirror is current and says decision-ready;
- the human decision point is clear.

## Known Gotchas

Things adopters will likely hit. Surfaced from the initial pilot.

- **`in_review` with `executionState: null` and `executionPolicy: null`.** A Paperclip issue can be `in_review` without ever having been routed through an execution policy. If the regimen assumes `currentParticipant` mutation works (as the routing primitive for child issues), this assumption will fail for issues that have no policy attached. Verify on the first child issue created: read the issue back after creation, inspect `executionState` and `executionPolicy`. If both are null, fall back to tagging the reviewer agent in a comment rather than mutating `currentParticipant`.
- **Reviewer tool availability varies by adapter.** Browser-automation tools (Playwright, screen recording, etc.) may not be available in every Paperclip adapter's runtime. Confirm the UI reviewer has access to browser automation **before** activating its child issue, not during.
- **Bulk activation burns budget.** If all child issues are moved to `todo` at once, every reviewer wakes simultaneously and the reviewer-budget cap is hit before findings can be observed. Always activate one lane at a time.
- **Role definitions inferred from job titles fail.** "CTO orchestrates" doesn't survive contact with reality when CTO is also the implementation owner. Always state the orchestrator's relationship to the PR/MR under review, not just their title.
- **A draft issue body in a separate tracking file is easier to iterate than the live Paperclip issue.** Author the issue body in a markdown file in your project's adoption notes; paste into Paperclip when ready. This avoids churning the live issue while wording is being refined.
- **Paperclip-only review state is invisible at merge time.** If completed lanes are only visible in Paperclip, the human reviewer has to leave the PR/MR to infer merge readiness. Require the orchestrator or plugin to mirror lane state onto the git-provider PR/MR after lane enumeration and after every lane state change.
- **Hidden external context breaks reviewer lanes.** If a reviewer needs information from a private knowledge base, wiki, or second brain, copy the durable relevant content into the repo, PR/MR, parent issue, or child issue before activation. Do not require reviewers to mount external notes at runtime.
- **Template updates are not agent updates.** A repo diff can describe the new output contract while live reviewer agents keep following their old behavior. Treat live-agent verification as part of adoption.

## Promotion To v1

Do not remove the preview warning until at least one real pilot retrospective has been written and reviewed.

Minimum promotion evidence:
- The pilot tracked orchestrator effort, child-issue state changes, reviewer budget, and time-to-decision.
- At least one reviewer decision included re-openable evidence that a human could inspect.
- The process handled either an iteration round, a documented no-finding rationale, a blocked lane, or an explicit bypass.
- The retrospective identified template changes, and those changes were applied or intentionally rejected with reasons.
- A second project can follow the adoption recipe without relying on project-specific names, paths, credentials, or hidden context.

When promoted, replace the preview warning with a stable-version note and link to the pilot retrospective as the canonical adoption example.

## See Also

- `paperclip-mr-review-adoption-recipe.md` — step-by-step recipe for a new project's first adoption.
- `paperclip-mr-review-matrix.md` — parent issue matrix template.
- `paperclip-review-agent-setup.md` — live reviewer-agent capability and setup guidance.
- `paperclip-review-child-issue.md` — per-lane child issue template.
- `paperclip-reviewer-output-contract.md` — required reviewer decision/evidence format.
- `paperclip-project-instructions.md` — general Paperclip board/project guidance.
