# Agent Team Starter Kit Adoption Checklist

Use this checklist in the adoption PR, issue, or completion evidence when installing the starter-kit pattern into a target project.

## Discovery

- [ ] Target repo/workspace identified
- [ ] Base branch/ref identified
- [ ] Existing agent instruction files inspected
- [ ] Existing issue/tracker templates inspected
- [ ] Existing docs/changelog/decision-log structure inspected
- [ ] Project tracker or agent board identified
- [ ] Secret-management convention identified or marked unknown
- [ ] External knowledge-base/runtime context boundary identified, if applicable

## Installed or updated artifacts

- [ ] Project-local agent instructions updated or created
- [ ] Source-of-truth split documented
- [ ] Agent issue / execution-contract guidance added
- [ ] Completion evidence guidance added
- [ ] Validation gates documented
- [ ] Changelog or decision-log convention documented
- [ ] UI/browser evidence guidance added when relevant
- [ ] Tool-specific instructions added only where the project already uses that tool
- [ ] For Paperclip PR/MR review: reviewer-agent setup installed or explicitly deferred
- [ ] For Paperclip PR/MR review: CEO/orchestrator heartbeat enabled or explicit exception recorded
- [ ] For Paperclip PR/MR review: heartbeat interval documented (5m pilot/incident, 15m normal, 30m mature low-traffic)
- [ ] For Paperclip PR/MR review: PR/MR status mirror convention documented
- [ ] For Paperclip PR/MR review: parent matrix, child issue, and reviewer output templates installed or referenced

## Safety checks

- [ ] No secrets, tokens, private keys, passwords, cookies, or production credentials added
- [ ] No private customer data added
- [ ] No private repository links added to public examples
- [ ] Sensitive actions require human approval
- [ ] Scope-expansion rule included

## Verification

- [ ] Markdown links checked
- [ ] New files are in the expected project locations
- [ ] Existing instructions were preserved rather than overwritten unnecessarily
- [ ] Adoption diff reviewed for accidental project-specific/private data
- [ ] Live tool state verified when adoption required live agents, boards, or tracker configuration
- [ ] Paperclip orchestrator heartbeat configuration read back from the live agent, if applicable
- [ ] Paperclip reviewer agents verified, if applicable, rather than assuming repo template changes updated them
- [ ] Human-facing summary includes files changed, assumptions, and remaining decisions

## Completion evidence

- Changed files:
- Templates installed or referenced:
- Validation performed:
- Assumptions:
- Remaining human decisions:
- Suggested first agent issue, if applicable:
