# Paperclip Git-Provider Webhook Plugin Plan

> **Status: experimental - preview.** Companion to `paperclip-mr-review-regimen.md`.

Use this template after a project has completed a manual Paperclip PR/MR review pilot and wants to automate the bridge between the git provider and Paperclip.

## Recommendation

Use the git provider's PR/MR webhook as the primary signal into a Paperclip plugin.

Use polling only as a scheduled reconciliation job that catches missed webhook deliveries, disabled webhooks, or Paperclip downtime.

Do not use Paperclip agent heartbeat as the detector. Heartbeat runs agents; it does not watch external git-provider state.

## Capability assumptions to verify

Before implementation, verify against the target Paperclip instance:

- `paperclipai plugin` can install and inspect plugins.
- The runtime supports inbound plugin webhooks (`webhooks.receive`).
- The runtime supports internal event subscriptions (`events.subscribe`) if the plugin will react to Paperclip issue updates.
- The runtime has an auditable webhook delivery record or equivalent log.
- No existing plugin already owns the same git-provider events.

Before implementation, verify against the git provider:

- PR/MR webhooks include open/update/merge/close actions.
- Payloads or API lookups expose project ID/path, PR/MR number, URL, head SHA, merge commit SHA, merged timestamp, and state.
- Webhook request authentication is available and can fail closed.
- Webhook retries include an idempotency or delivery identifier, or the plugin can derive one from payload fields.

## Plugin scope

Primary responsibilities:

1. Receive PR/MR webhook events from the git provider.
2. Resolve the linked Paperclip parent issue from PR/MR metadata.
3. Maintain the git-provider PR/MR status mirror from Paperclip child review state.
4. Close out the parent Paperclip issue when a reviewed PR/MR is merged.
5. Reset affected review lanes when a PR/MR receives a new head SHA.
6. Run scheduled reconciliation as a safety net.

Non-goals:

- Do not merge PRs/MRs.
- Do not expose the Paperclip UI or core API publicly.
- Do not require reviewer agents to mount a private knowledge base or second brain.
- Do not store sensitive review artifacts in the product repo.

## Inbound webhook flow

Endpoint shape:

```text
POST /api/plugins/<plugin-key>/webhooks/<git-provider>
```

Handler behavior:

1. Validate the provider signature or shared secret. Missing or invalid auth returns `401`.
2. Record the delivery in Paperclip's plugin delivery log.
3. Acknowledge quickly with `200` or `202`; long work should run after receipt, not in the request path.
4. Build an idempotency key:

```text
<provider>:<project-id>:prmr:<number>:<action>:<head-or-merge-sha>:<delivery-id>
```

5. If the delivery was already processed, return success without mutating state again.
6. Resolve the parent Paperclip issue.
7. Apply the event-specific mutation.

## Parent issue resolution

Preferred lookup order:

1. PR/MR description contains a Paperclip issue token, e.g. `ABC-123`.
2. PR/MR comments contain the review matrix or approval packet with the parent issue ID.
3. Paperclip parent issue contains the exact PR/MR URL.
4. Fallback: search open/in-review Paperclip issues for the PR/MR URL and project path.

If more than one parent matches, the plugin must not guess. It records a blocked delivery requiring human resolution.

## Event behavior

| Git-provider event | Paperclip behavior |
|---|---|
| PR/MR opened | If parent is linked and not already in review, move parent to `in_review`; enumerate lanes only when the Phase 2 lane classifier is enabled. |
| PR/MR updated with new head SHA | Compare new SHA with reviewed SHA; reset affected child lanes; update parent matrix and PR/MR mirror to `not decision-ready`. |
| PR/MR approved/unapproved | Mirror only if useful; do not treat git-provider approval as Paperclip lane approval. |
| PR/MR merged | Mark the parent Paperclip issue `done`; post close-out comment with PR/MR URL, merge commit SHA, deploy info if available, final approval packet link, and webhook delivery ID. |
| PR/MR closed without merge | Move parent back to active review state or blocked state per regimen rule; post reason and close metadata. |
| Webhook delivery duplicate | No-op after idempotency check. |
| Unknown action | Record delivery and ignore unless it changes SHA/state; never fail open into a mutation. |

## Merge close-out comment

The plugin should post a parent issue comment in this shape:

```markdown
## PR/MR Close-Out

PR/MR: <url>
State: merged
Merge commit: <sha>
Merged at: <timestamp>
Deployment: <deploy timestamp/status if known, otherwise "not checked by plugin">
Final approval packet: <Paperclip parent matrix or git-provider comment URL>
Webhook delivery: <delivery id>

Paperclip parent status set to `done`.
```

The mutation is idempotent. If the parent is already `done` with the same merge SHA, update only if the existing close-out lacks required evidence.

## Scheduled reconciliation

Polling is a backup, not the source of truth.

Recommended cadence:

- Every 15 minutes during the pilot.
- Every 30-60 minutes after the plugin has proved stable.

The reconciliation routine:

1. Finds Paperclip parent issues in `in_review` with linked PRs/MRs.
2. Queries the git provider's PR/MR state by project + number.
3. If the provider says merged and Paperclip is not `done`, runs the same close-out path as the webhook.
4. If provider head SHA differs from the parent matrix reviewed SHA, runs the lane reset path.
5. Records a reconciliation note only when it mutates state or detects an inconsistency.

## Security requirements

- Prefer cryptographic request signing when the provider supports it; otherwise validate the strongest available shared-secret header.
- Reject unsigned requests. No soft-fail behavior.
- Expose only declared plugin webhook paths through public ingress.
- Keep Paperclip UI, board auth, core API, and agent dispatch private.
- Store provider secrets in Paperclip/plugin secret storage, not repo files.
- Treat IP allowlisting as defense in depth, not a substitute for signature/token verification.

## Acceptance tests

Before using this on live PRs/MRs:

- A fake update event with a new head SHA resets only affected child lanes.
- A fake merge event closes a test parent issue and writes the close-out comment.
- Replaying the same merge event creates no duplicate comments or state churn.
- An invalid signature/token returns `401` and produces no Paperclip mutation.
- A linked PR/MR with ambiguous parent issue matches blocks instead of guessing.
- Reconciliation detects a merged PR/MR missed by webhook and closes the parent.
- Public ingress returns `404` for Paperclip UI/core API paths on the webhook hostname.

## Implementation issue prompt

```text
Build git-provider PR/MR webhook plugin for Paperclip review close-out

Acceptance criteria:
- Plugin manifest declares inbound webhook capability and any needed event/routine capability.
- PR/MR webhook endpoint validates request auth fail-closed.
- Merge events idempotently close linked Paperclip parent issues.
- Update events detect changed head SHA and reset affected lanes.
- Parent matrix and git-provider PR/MR status mirror are refreshed after plugin mutations.
- Scheduled reconciliation catches missed merge/update state.
- Tests or replay scripts cover valid merge, duplicate merge, invalid auth, ambiguous parent, and missed-webhook reconciliation.
```
