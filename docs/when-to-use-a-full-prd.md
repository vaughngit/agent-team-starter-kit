# When to Use a Full PRD

Default to a mini-PRD and structured agent issues. Do not make every agent task wait for a heavyweight product requirements document.

Use a full PRD only when the decision needs more governance, shared context, or long-term product framing than a compact issue contract can provide.

## Use a mini-PRD when

A mini-PRD is usually enough when:

- One human owner can clarify intent and accept the result.
- The work can be split into one or a few agent issues.
- The risk is manageable with normal validation gates.
- The output is a branch, PR/MR, document, prototype, or reviewable artifact.
- The main need is to clarify scope, acceptance criteria, evidence, and non-goals.

Use `templates/mini-prd-template.md` for this case.

## Promote to a full PRD when

Consider a full PRD when one or more are true:

- Multiple stakeholders need to agree on the problem or outcome.
- The work changes a core workflow, business process, or platform direction.
- The change is expensive or difficult to reverse.
- The work carries production, security, compliance, financial, customer-data, or safety risk.
- Several implementation paths are plausible and the decision rationale should be recorded.
- Multiple agent teams or multiple phases will work from the same intent.
- Future reviewers need a single narrative without reading the entire issue/comment trail.
- Human approval is needed for merge, deploy, launch, activation, or other sensitive next steps.

## Full PRD outline

A full PRD can still be concise. Include only what is useful.

```markdown
# PRD: <Title>

## 1. Executive summary
- What is being proposed?
- What decision is being requested?
- What is not being approved by this PRD?

## 2. Problem statement
- Current problem:
- Why now:
- Users / systems affected:

## 3. Goals and non-goals
- Goals:
- Non-goals:

## 4. Stakeholders and roles
- Human owner:
- Reviewer / approver:
- Implementation agent/team:
- QA/reviewer agent/team:

## 5. Current state
- Current workflow/system:
- Known pain points:
- Relevant source-of-truth links:

## 6. Proposed approach
- New behavior / workflow:
- Key design decisions:
- Alternatives considered:

## 7. Requirements
- Functional requirements:
- Non-functional requirements:
- Data/security/privacy requirements:
- Documentation requirements:

## 8. Acceptance and validation plan
- Required tests/checks:
- Required artifacts:
- Reviewer expectations:
- Evidence that must be posted back to issues or PR/MR:

## 9. Rollout / next-step plan
- Implementation phases:
- Human decision points:
- Merge/deploy/activation criteria:
- Rollback or stop condition, if applicable:

## 10. Risks and mitigations
- Risk:
- Mitigation:
- Owner:

## 11. Open questions
- ...

## 12. Evidence appendix
- Issues:
- PRs/MRs:
- Changelog / decision log:
- Test output / screenshots / traces:
```

## Promotion checklist

Before promoting a mini-PRD to a full PRD, confirm:

- [ ] The mini-PRD is no longer enough for the decision being made.
- [ ] The stakeholders or reviewers are known.
- [ ] The acceptance criteria and evidence needs are known or explicitly open.
- [ ] The source-of-truth links are listed.
- [ ] The sensitive approval boundary is explicit.
- [ ] The full PRD will reduce ambiguity rather than create process drag.

## Anti-patterns

- Writing a full PRD because the task feels important but nobody needs the extra structure.
- Letting the PRD replace the issue contract; agents still need scoped issues.
- Treating PRD approval as automatic approval to merge, deploy, or activate unless that is explicitly stated.
- Hiding unresolved decisions in vague language instead of listing open questions.
