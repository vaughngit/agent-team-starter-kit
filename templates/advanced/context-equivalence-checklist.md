# Advanced: Context Equivalence Checklist

Use this only for migrations, rewrites, workflow replacements, platform changes, or other cases where a new agentic/system path must preserve behavior from an existing path.

This is not required for ordinary feature work. The goal is to avoid replacing a workflow while accidentally dropping hidden context that the old workflow depended on.

```markdown
# Context Equivalence Checklist: <Workflow/System>

## 1. Current workflow being replaced

- Workflow name:
- Current owner:
- Current entrypoint(s):
- Current schedule or trigger:
- Current environment/runtime:
- Current outputs:

## 2. Current context sources

List what the existing workflow reads or depends on.

### Docs / instructions
- [ ] README / runbook:
- [ ] AGENTS.md / agent instructions:
- [ ] Tool-specific instruction files:
- [ ] Product/design notes:
- [ ] Historical docs that might be stale:

### Code / config
- [ ] Scripts / wrappers:
- [ ] Config files:
- [ ] Environment variables:
- [ ] Feature flags:
- [ ] Service dependencies:

### Data / state
- [ ] Database tables / records:
- [ ] Files / local state:
- [ ] Logs / history:
- [ ] External APIs:
- [ ] User/customer data boundaries:

### Outputs / side effects
- [ ] Files written:
- [ ] API calls made:
- [ ] Notifications sent:
- [ ] Commits/PRs/MRs created:
- [ ] Production or user-visible mutations:

## 3. New workflow mapping

For each current context source, state how the new workflow handles it.

| Current source | Current purpose | New source/field/path | Preserved? | Evidence |
|---|---|---|---|---|
| | | | yes/no/N/A | |

## 4. Stale or conflicting instructions

Classify old instructions so agents do not blindly ingest them.

| Source | Classification | How to handle |
|---|---|---|
| | active/current | |
| | historical/context only | |
| | deprecated/do not use | |
| | tool-specific | |
| | unsafe/stale | |

## 5. Validation gates

- [ ] Inventory test/check covers all known context sources.
- [ ] New workflow fixture includes the required context categories.
- [ ] Stale/deprecated instructions cannot override current rules.
- [ ] Side effects are disabled or safely gated during validation.
- [ ] Output comparison is documented.
- [ ] Human reviewer can see what changed and what did not.

## 6. Human decision

- [ ] Keep old workflow active.
- [ ] Continue shadow/parity testing.
- [ ] Approve partial replacement.
- [ ] Approve full replacement.
- [ ] Stop and investigate gaps.
```

## When not to use this

Do not use this checklist for small isolated fixes, copy-only changes, or normal feature work where there is no old workflow being replaced.
