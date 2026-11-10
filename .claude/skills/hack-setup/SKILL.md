---
name: hack-setup
description: "Run once after cloning: sets up your lane, branch, git hooks and inbox, then briefs you on what you own and what to build first."
disable-model-invocation: true
---

1. Run `scripts/hack setup`. If it says your login has no lane, show the user the lane list it printed, ask which is theirs, and run `scripts/hack setup <lane>`.
2. If it reports a missing tool or `gh` login, give the user the exact command it printed and stop until that's done.
3. Read, in this order: `AGENTS.md`, `docs/DEMO.md`, `docs/lanes/<your lane>/CONTRACT.md`, and the CONTRACT of each lane yours consumes. Read the map issue (`gh issue list --label hack:map`).
4. Brief the user in under ten lines: your lane and folders · the beats you carry · what you expose and to whom · your open tickets in priority order · the one command loop (`scripts/hack run` → `scripts/hack ship`).
5. Explain in two lines how they'll work: this one chat is their orchestrator, background workers build the `ready-for-agent` tickets, and anything that needs them appears under **Needs you**. Ask whether to start. On yes, call the Skill tool with "hack-orchestrate".

The lane guard, auto-sync and inbox hooks start working the moment setup finishes; nothing needs restarting.
