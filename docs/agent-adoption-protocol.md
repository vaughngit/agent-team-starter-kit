# Agent Adoption Protocol

Use this protocol when a human points an AI assistant, coding agent, or agent board at this starter kit and says to adopt it into another project.

The goal is turnkey adoption: do not make the human manually copy template text, interpret the framework, or decide where every artifact belongs. If the agent has access to the target project, the agent should inspect the project, make the smallest safe changes, and return a patch or PR with receipts.

## Activation phrase

Treat any request like these as an adoption request:

- "Adopt the Agent Team Starter Kit in this repo."
- "Set this project up to use the starter kit."
- "Use this starter kit for our agents."
- "Point my agent at this repo and make my project follow it."

## Agent behavior

When adopting this kit:

1. Act, do not only summarize.
2. Inspect the target project before writing files.
3. Reuse existing project conventions where possible.
4. Add project-local instructions that future agents will actually read.
5. Keep the framework tool-agnostic unless the target project clearly uses a specific tool.
6. Do not expose secrets, private customer data, private repo links, or production credentials.
7. Ask the human only when a decision cannot be inferred safely.
8. Return changed files, validation performed, and remaining manual decisions.

## Inputs the agent should determine

Before editing, identify:

- Target repo/workspace path or remote URL
- Primary branch or base ref
- Existing agent instruction files, such as:
  - `AGENTS.md`
  - `CLAUDE.md`
  - `.cursorrules`
  - `.cursor/rules/*`
  - `.github/copilot-instructions.md`
  - project README or runbook
- Existing issue template locations, such as:
  - `.github/ISSUE_TEMPLATE/`
  - `.gitlab/issue_templates/`
  - tracker-specific docs
- Existing docs structure, such as:
  - `docs/`
  - `docs/decisions/`
  - `docs/requirements/`
  - `CHANGELOG.md`
- Tracker or board used by the project:
  - Paperclip
  - GitHub Issues
  - Linear
  - Jira
  - other
- Secret-management convention, if documented

If the target project is not accessible, return a ready-to-apply patch plan instead of pretending adoption happened.

## Adoption steps

### 1. Add or update project-local agent instructions

Create or update the instruction file future agents are most likely to read.

Preferred order:

1. Existing `AGENTS.md`
2. Existing tool-specific instruction file
3. New `AGENTS.md`
4. README section only if no agent instruction file is appropriate

Use `templates/adoption/AGENTS.md-snippet.md` as the canonical project-local block.

### 2. Add source-of-truth guidance

The target project must tell agents what each system owns.

At minimum, define:

- Human owner: intent, risk tolerance, final acceptance, sensitive-action approval
- Issue tracker / agent board: live execution state, assignments, blockers, completion evidence
- Repo: code, tests, repo-local docs, changelog, decisions close to implementation
- Knowledge base / external docs: broader planning, long-form rationale, cross-project context
- Secret manager: secret values and credential metadata only
- PR/MR/code-review surface: diffs, review discussion, CI, review artifacts

If the target project uses a private knowledge base, wiki, or second brain, define the runtime boundary explicitly: planning context may live there, but context required by implementation or review agents must be copied or summarized into the repo, issue, or PR/MR before activation.

If the target project already has a source-of-truth model, preserve it and add missing starter-kit requirements.

### 3. Add issue or tracker guidance

If the project has issue templates, add or update an agent-work template using `templates/agent-issue-template.md`.

If the project uses a remote tracker where files cannot be edited directly, add a repo-local doc that tells the supervising agent how to create issues from the template.

Every agent issue should include:

- Problem or intent
- Acceptance criteria
- Scope boundaries
- Safety boundaries
- Validation gates
- Changelog/docs impact
- Completion evidence expectations
- Human decision point

### 4. Add completion-evidence guidance

Add or reference `templates/completion-evidence-template.md` so implementation agents know what receipts to return.

Completion evidence must include:

- Branch, commit, PR/MR, or review artifact
- Validation commands and results
- UI/browser evidence when applicable
- Data and mutation boundary
- Docs/changelog update or `N/A` reason
- Follow-up issues
- Human decision needed

### 5. Add changelog or decision-log convention

If the repo has `CHANGELOG.md`, use it.

If it has decision records, use the existing decision-record directory.

If neither exists, create one lightweight convention. Suggested defaults:

- `CHANGELOG.md` for user/operator-visible changes and multi-issue milestones
- `docs/decisions/` for architecture decisions
- `docs/change-management/` for rollout, validation, rollback, or activation notes
- `docs/requirements/` for repo-safe mini-PRDs or requirements agents need near the code

Do not turn every small change into bureaucracy. Mark changelog `N/A` with a reason for one-off tiny changes.

### 6. Add validation gates

Make validation explicit enough that future agents can run it without guessing.

Include gates for:

- Static/code inspection
- Build/lint/test
- Backend/API validation, if applicable
- UI/browser validation, if applicable
- Regression checks
- Deployment/smoke checks, if applicable
- Documentation/changelog impact
- Completion evidence

### 7. Add tool-specific integration only when detected

Use the adoption templates that match the target project:

- `templates/adoption/CLAUDE.md` for Claude Code-style repos
- `templates/adoption/copilot-instructions.md` for GitHub Copilot instructions
- `templates/adoption/cursor-rules.md` for Cursor rules
- `templates/adoption/paperclip-project-instructions.md` for Paperclip boards/projects
- `templates/adoption/linear-issue-guidance.md` for Linear-backed planning

Do not add every tool-specific file by default. Prefer the project’s existing tool surface.

For Paperclip-backed PR/MR review, adoption is incomplete unless the agent also installs or references:

- `templates/adoption/paperclip-mr-review-regimen.md`
- `templates/adoption/paperclip-mr-review-adoption-recipe.md`
- `templates/adoption/paperclip-mr-review-matrix.md`
- `templates/adoption/paperclip-review-agent-setup.md`
- `templates/adoption/paperclip-review-child-issue.md`
- `templates/adoption/paperclip-reviewer-output-contract.md`

The adopting agent must verify that live reviewer agents know the reviewer output contract and the PR/MR status mirror rule. A template change alone does not update the agents that Paperclip will wake.

If the target project has a git-provider PR/MR surface, document where lane state is mirrored there. Paperclip issue state is the detailed system of record, but the PR/MR must show current review readiness because the human merge decision happens there.

### 8. Return receipts

At the end, report:

- Files created or modified
- Which starter-kit templates were installed or referenced
- Validation performed
- Assumptions made
- Any remaining human decisions
- Suggested first agent issue, if appropriate

For Paperclip PR/MR review adoptions, also report whether live reviewer agents were updated or only a patch plan was produced. Do not imply live-agent behavior changed when only repo files changed.

## Adoption checklist

Use `templates/adoption/adoption-checklist.md` as the checklist for the adoption PR or issue.

## Success criteria

Adoption is successful when a future agent can start in the target project, read its local instructions, and know:

- how to turn rough intent into a structured issue
- where source-of-truth information belongs
- what evidence to collect
- when to update docs/changelogs
- what not to change without approval
- what human decision is required before done, merge, deploy, or activation
- if using Paperclip review lanes, how reviewer decisions become visible on the PR/MR and where reviewer agents get their runtime context
