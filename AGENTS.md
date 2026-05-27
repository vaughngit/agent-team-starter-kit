# AGENTS.md

This repo contains templates for shaping autonomous agent work into reviewable issues, validation gates, and completion evidence.

When an AI assistant or coding agent works in this repo:

1. Preserve the core framing: agent teams need handoffs, not just prompts.
2. Emphasize the team workflow:
   - human provides mini-PRD / user story / rough vision
   - local assistant or supervisor agent turns it into a structured issue
   - implementation agent works from the issue contract
   - reviewer/QA agent checks the receipts
   - human accepts or rejects the evidence
3. Keep templates tool-agnostic. Mention Paperclip, GitHub Issues, Linear, Jira, Copilot, Codex, Claude, and Hermes only as examples unless a file is explicitly tool-specific.
4. Do not include private customer data, secrets, production credentials, private repo links, or app-specific implementation details.
5. Public examples must use synthetic data and generic product names.
6. The changelog component is part of the starter kit. Use `templates/changelog-entry-template.md` and `docs/changelog-workflow.md` when work is dependent, multi-issue, production-sensitive, or needs a downstream activation decision.
7. When creating new templates, include:
   - routing / ownership
   - problem or intent
   - acceptance criteria
   - scope boundaries
   - safety boundaries
   - validation gates
   - completion evidence
   - human decision point
8. Use `templates/mini-prd-template.md` for rough intent that needs to become agent-ready work, but keep it lightweight.
9. Use `docs/when-to-use-a-full-prd.md` only when broader review, formal approval, or long-term product framing is actually useful.
10. Keep migration/cutover-specific guidance optional or advanced; do not make the starter kit feel like a production cutover framework.
11. For UI workflows, require browser-visible evidence such as screenshots, traces, or exact manual QA steps.
12. Make it clear that Paperclip or any other agent board only uses these templates if they are copied into an issue, linked in project docs, or referenced by project/agent instructions.
13. Maintain the turnkey adoption path: `docs/agent-adoption-protocol.md` is the agent-facing entrypoint for installing this framework into a target project without the human manually copying instructions.
14. When adding adoption guidance, prefer concrete files, snippets, checklists, and receipt requirements over conceptual prose.
15. When describing Hermes posting issues to Paperclip, say Hermes uses board-authorized authentication as the board user/operator.
