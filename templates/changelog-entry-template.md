# Changelog Entry Template

Use this when agent work is part of a multi-step initiative, dependent issue ladder, production change, or anything where future agents need to understand what actually happened.

A changelog is not just a release note. It is the durable narrative layer between live issue state and future agent context.

```markdown
# <YYYY-MM-DD>: <Initiative or issue title>

## Status

- Current status: planned | active | in_review | accepted | deployed | blocked | cancelled
- Related issue(s):
- Related PR/MR(s):
- Owner / reviewer:
- Last updated:

## Plain-English summary

What changed, why it mattered, and what a future human or agent should understand first.

## Source of truth

- Issue / tracker link:
- PR/MR/review artifact:
- Project docs / agent contract:
- Related decision notes:

## What landed

- ...
- ...

## Validation evidence

| Gate | Evidence | Result |
|------|----------|--------|
| Intent / acceptance criteria | | pass/fail/N/A |
| Automated tests / build / lint | | pass/fail/N/A |
| Backend / API / data | | pass/fail/N/A |
| UI / browser / screenshots | | pass/fail/N/A |
| Regression / adjacent checks | | pass/fail/N/A |
| Deployment / smoke | | pass/fail/N/A |

## Safety and data boundaries

- Data touched:
- Mutations performed:
- Fixtures used:
- Secrets/credentials involved:
- Human approvals required:

## Open questions / blockers

- ...

## Follow-up work

- [ ] ...
- [ ] ...

## Downstream activation decision

Can the next issue/agent be activated?

- Decision: yes | no | blocked | needs human review
- Reason:
- Next issue, if any:

## Done log

- `[DONE] <issue-or-PR> — <one-line summary>`
```
