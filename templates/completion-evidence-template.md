# Completion Evidence Template

Use this as the final comment or handoff note when an agent says work is ready for review.

```markdown
## Completion evidence

### Summary

- Issue:
- One-line outcome:
- Scope completed:
- Scope intentionally not completed:

### Branch / review artifact

- Branch:
- Commit(s):
- PR/MR/review artifact:
- Base branch:

### Validation commands and results

| Gate | Command / method | Result | Evidence |
|------|------------------|--------|----------|
| Static/code inspection | | pass/fail/N/A | |
| Build/lint/test | | pass/fail/N/A | |
| Backend/API | | pass/fail/N/A | |
| UI/browser | | pass/fail/N/A | |
| Regression | | pass/fail/N/A | |
| Deployment/smoke | | pass/fail/N/A | |

### UI/browser evidence, if applicable

- Browser path/URL:
- Fixture/harness used:
- Interaction sequence:
- Screenshots/traces:
- Temporary harness removed before commit: yes/no/N/A

### Safety / data boundary

- Data touched:
- Mutations performed:
- Secrets used by pointer only:
- Human approval required before next step:

### Review notes

- Reviewer should inspect:
- Known limitations:
- Follow-up issues:
- Deployment/cache/migration notes:

### Done criteria

- [ ] PR/MR is reviewed or explicitly accepted.
- [ ] Deployment/merge status is clear.
- [ ] Required docs/changelog are updated.
- [ ] Follow-up work is captured instead of hidden.
- [ ] Issue is not marked done until evidence is accepted.
```
