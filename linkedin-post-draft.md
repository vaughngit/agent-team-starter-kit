# LinkedIn post draft: Agent brought receipts + starter kit

The agent brought receipts.

The part I care about is not autonomous code generation by itself.

It is what happens after the agent finishes: the verification loop around autonomous code work.

Did it understand the issue?
Did it test the right behavior?
Did it run in the right environment?
Did it produce evidence a human can review without replaying the whole task from scratch?

The workflow I want is simple:

- human gives the vision
- the vision becomes a structured issue / execution contract
- focused agents work in focused context windows
- the branch or merge request becomes the handoff unit
- validation gates are explicit
- screenshots, logs, test output, and review notes come back with the work
- the human reviews receipts, not claims

A recent UI task made this click for me.

The agent did not just say “tested.”

It used browser automation against a safe fixture, checked the relevant UI states, captured screenshots, and attached the evidence to the merge request.

That changed the review from:

“the agent says it worked”

to:

“I can inspect the evidence.”

I put together a small starter kit for this pattern: issue templates, validation gates, UI evidence checklists, and completion evidence prompts that people can adapt for their own agent teams.

Repo: https://github.com/vaughngit/agent-team-starter-kit

That is the direction I want agentic workflows to go.

Not autonomous agents silently shipping work.

Autonomous agents returning with receipts.

Question: if you were reviewing AI-agent work on a production system, what evidence would you require before trusting it?

For folks interested in the stack I am testing:
- Paperclip: https://github.com/paperclipai/paperclip
- Hermes Agent: https://github.com/NousResearch/hermes-agent

## First comment option

Starter kit: https://github.com/vaughngit/agent-team-starter-kit

It includes:
- agent issue / execution contract template
- UI evidence validation template
- completion evidence template
- example synthetic issue
- lightweight agent team operating model
