---
name: hack-setup
description: "Run once after cloning: sets up your lane, branch, git hooks and inbox, then briefs you on what you own and what to build first."
disable-model-invocation: true
---

1. Run `scripts/hack setup`. If it says your login has no lane, show the user the lane list it printed, ask which is theirs, and run `scripts/hack setup <lane>`.
2. If it reports a missing tool or `gh` login, give the user the exact command it printed and stop until that's done.
3. Read, in this order: `AGENTS.md`, `docs/DEMO.md`, `docs/lanes/<your lane>/CONTRACT.md`, and the CONTRACT of each lane yours consumes. Read the map issue (`gh issue list --label hack:map`).
4. Brief the user in under ten lines: your lane and folders · the beats you carry · what you expose and to whom · your open tickets in priority order · the one command loop (`scripts/hack run` → `scripts/hack ship`).
5. Tell them what comes next, as commands:
   - The lane's **stub ticket** already exists and is `ready-for-agent`. Offer to start a worker on it right away, so the other lanes are unblocked while the user designs.
   - The rest of the lane is theirs to design, in this chat: `/grill-me` (talk through how the lane delivers its beats) → `/hack-spec` (write the design down) → `/hack-tickets` (split it into tickets) → `/hack-orchestrate` (workers build them; this chat tests and ships).
   - Anything that needs them appears under **Needs you**.

The lane guard, auto-sync and inbox hooks start working the moment setup finishes; nothing needs restarting.
