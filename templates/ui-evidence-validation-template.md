# UI Evidence Validation Template

Use this when an agent changes visible behavior, safety affordances, disabled/enabled states, confirmations, warnings, dropdowns, layout, color/contrast, or any workflow where a screenshot would materially improve review.

```markdown
## UI Evidence Validation Contract

### What must be proven

- [ ] The relevant UI context is visible.
- [ ] The expected before-state is visible.
- [ ] The critical interaction is driven with real browser events.
- [ ] The expected after-state is visible.
- [ ] Any sensitive or destructive action remains guarded until the required confirmation/input is present.
- [ ] The evidence uses synthetic/safe fixture data unless a human explicitly approved real data.

### Fixture plan

- Existing safe fixture available? yes/no
- If no, temporary local-only harness needed? yes/no
- Fixture data:
- Mutation boundary:
- API routes mocked/intercepted:
- Temporary files/routes to remove before commit:

### Browser automation plan

- Tool: Playwright | Cypress | browser automation | manual with screenshots
- Browser:
- URL or local route:
- Interaction sequence:
  1. ...
  2. ...
  3. ...

### Required screenshots/traces

- [ ] Screenshot 1: context / initial state
- [ ] Screenshot 2: guarded or disabled state
- [ ] Screenshot 3: enabled or success state
- [ ] Trace/video required? yes/no

### Commands to run

```bash
# install/build/start commands

# browser validation command

# cleanup/verification command
```

### Expected evidence in PR/MR/comment

- Command output summary:
- Screenshot paths or uploaded links:
- Trace/video path, if any:
- Fixture data summary:
- API intercept/mock summary:
- Confirmation that temporary harness was removed before commit:
- Any remaining manual QA steps:
```

## Reviewer checklist

- [ ] I can understand what the UI is supposed to prove.
- [ ] The screenshots match the acceptance criteria.
- [ ] The fixture data is safe and deterministic.
- [ ] The interaction sequence covers the risk, not just page rendering.
- [ ] Temporary harness/debug code was removed before commit.
- [ ] Any untested behavior is called out as a follow-up or manual QA exception.
