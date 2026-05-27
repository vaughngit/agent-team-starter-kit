# Adoption Templates

These files help an AI agent install the Agent Team Starter Kit into a target project without the human manually copying instructions.

Start with `docs/agent-adoption-protocol.md`, then use the files here that match the target project.

## Files

- `AGENTS.md-snippet.md` — canonical project-local instructions to add to a target repo's `AGENTS.md` or equivalent.
- `adoption-checklist.md` — checklist for the adoption PR, issue, or completion evidence.
- `CLAUDE.md` — Claude Code-style project instructions.
- `copilot-instructions.md` — GitHub Copilot instruction content.
- `cursor-rules.md` — Cursor rules content.
- `paperclip-project-instructions.md` — Paperclip board/project guidance.
- `paperclip-mr-review-regimen.md` — experimental multi-agent PR/MR review regimen for Paperclip-backed projects (status: preview; see file header).
- `paperclip-mr-review-adoption-recipe.md` — step-by-step recipe for a new project's first adoption of the regimen.
- `paperclip-mr-review-matrix.md` — parent issue matrix for tracking required review lanes and decisions.
- `paperclip-review-child-issue.md` — child issue template for a single review lane.
- `paperclip-reviewer-output-contract.md` — required decision/evidence format for review-lane agents.
- `linear-issue-guidance.md` — Linear issue guidance.

## Agent rule

Do not install every tool-specific file by default. Inspect the target project, preserve existing conventions, and add only the instructions that future agents in that project are likely to read.
