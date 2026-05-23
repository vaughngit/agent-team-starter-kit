# AGENTS.md

This repo contains templates for shaping autonomous agent work into reviewable issues, validation gates, and completion evidence.

When an AI assistant or coding agent works in this repo:

1. Preserve the core framing: agent teams need handoffs, not just prompts.
2. Emphasize the team workflow:
   - human provides PRD / user story / rough vision
   - local assistant or supervisor agent turns it into a structured issue
   - implementation agent works from the issue contract
   - reviewer/QA agent checks the receipts
   - human accepts or rejects the evidence
3. Keep templates tool-agnostic. Mention Paperclip, GitHub Issues, Linear, Jira, Copilot, Codex, Claude, and Hermes only as examples unless a file is explicitly tool-specific.
4. Do not include private customer data, secrets, production credentials, private repo links, or app-specific implementation details.
5. Public examples must use synthetic data and generic product names.
6. When creating new templates, include:
   - routing / ownership
   - problem or intent
   - acceptance criteria
   - scope boundaries
   - safety boundaries
   - validation gates
   - completion evidence
7. For UI workflows, require browser-visible evidence such as screenshots, traces, or exact manual QA steps.
8. Make it clear that Paperclip or any other agent board only uses these templates if they are copied into an issue, linked in project docs, or referenced by project/agent instructions.
9. When describing Hermes posting issues to Paperclip, say Hermes uses board-authorized authentication as the board user/operator. Never include token values, credential strings, or private 1Password item details in this public repo.
