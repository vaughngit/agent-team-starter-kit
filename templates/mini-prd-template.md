# Mini-PRD Template

Use this when a human has a rough idea, user story, bug, or product note that needs to become agent-ready work.

The goal is not to create a heavyweight requirements process. The goal is to give the local assistant, supervisor agent, implementation agent, reviewer, and human owner the same compact understanding of intent, boundaries, and evidence.

```markdown
# Mini-PRD: <Title>

## 1. Problem / opportunity

What problem, user pain, operational gap, or product opportunity are we addressing?

- Current situation:
- Why it matters:
- Who is affected:

## 2. Desired outcome

What should be true when this is done?

- User-visible outcome:
- Business / operational outcome:
- Engineering outcome:

## 3. Scope

In scope:
- ...

Out of scope:
- ...

Non-goals:
- ...

## 4. Constraints and boundaries

- Repo / workspace:
- Base branch or environment:
- Data that must not be touched:
- Secrets policy:
- Safe fixture / test-data plan:
- Human approval required before:

## 5. Acceptance criteria

- [ ] The expected behavior is explicit and testable.
- [ ] Edge cases or important exclusions are named.
- [ ] The agent can complete the work without guessing at scope.
- [ ] The reviewer can judge the result from evidence.

Specific criteria:
- [ ] ...
- [ ] ...
- [ ] ...

## 6. Evidence required

What receipts must the agent return before the work is considered reviewable?

- Code / diff artifact:
- Test/build/lint output:
- API/data evidence:
- UI/browser evidence, if applicable:
- Screenshots/traces, if applicable:
- Docs/changelog update, if applicable:
- Known limitations / follow-ups:

## 7. Source-of-truth links

- Issue tracker / board:
- Repo / workspace:
- Project docs / README / AGENTS.md:
- Design / product notes:
- Changelog / decision log:
- Credential pointers, without secret values:

## 8. Human decision needed

What decision should the human make after reviewing the evidence?

- Accept as done:
- Request changes:
- Approve merge/deploy:
- Activate next issue:
- Defer / cancel:

## 9. Agent issue handoff

Use this mini-PRD to create one or more structured agent issues. Each issue should include:

- routing / ownership
- problem or intent
- acceptance criteria
- scope boundaries
- safety boundaries
- validation gates
- completion evidence fields
```

## Usage notes

Use the mini-PRD when the work is bigger than a one-line bug but smaller than a full product spec. If the work needs multiple stakeholders, formal approval, production-risk review, or long-term product framing, see `docs/when-to-use-a-full-prd.md`.
