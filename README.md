# Hackathon Kit

A Claude Code setup for a four-person hackathon team: four lanes, zero merge conflicts, one-command run and ship, and every decision made from the judge's seat. Built on [Matt Pocock's skills](https://github.com/mattpocock/skills) (MIT), adapted for a deadline.

## Teammate: 3 steps

```bash
gh repo clone <org>/<repo> && cd <repo>
```

Open the folder in Claude Code, then:

```
/hack-setup
```

That's it. You're on your lane's branch, your agent knows what it owns, and it tells you your first ticket. Needs `git`, `gh` (logged in) and `jq`; Xcode only if you want to run an iOS app.

## Lead: before and at the hackathon

| When | Run | What happens |
|------|-----|--------------|
| Before | `/hack-init` | Creates the GitHub repo from this kit: `dev` (default) + `prod` branches, labels, auto-merge. Invites the team. |
| Kick-off | `/hack-wayfinder` | Brief → ideas scored for wow + demonstrability → 90-second demo script → **exactly four lanes** → map issue + tickets. |
| During | `/hack-promote` | Runs the golden path; if every beat performs, moves `prod` forward and tags it. |
| After | `/hack-retro` | Turns everything that went wrong with the kit (logged live by the agents with `scripts/hack feedback`) into clean issues on the kit repo, for next time. |

## The daily loop (everyone)

You use **one chat**. It is your lane's orchestrator: it hands `ready-for-agent` tickets to background workers, merges and runs their work, ships it, and answers with a small board: **Building / Ready to test / Needs you**. You never open or manage the workers.

```
"work the queue"              → hack-orchestrate: dispatch workers → collect → run → ship
"work the next ticket"        → hack-implement: build one ticket yourself, no workers
"run it"  or  /hack-run      → build + launch in Claude's simulator panel, beside the chat
scripts/hack run              → the same from a terminal (opens the Simulator window)
"what if we changed X?"       → hack-direction: judge compares before/after → you sign off → every agent is told
scripts/hack feedback "what"  → log a problem with the kit itself, for after the event
scripts/hack ship "msg"       → commit → pull dev → PR → merged
scripts/hack request <lane> "need X"   → ask another lane; keep working on a stub
```

## How it prevents merge conflicts

1. **Lanes.** `.hack/lanes.json` gives every folder exactly one owner. Your agent is blocked from writing anywhere else (Claude hook), your commits are checked (git hook), and PRs are checked (CI).
2. **Contracts + stubs.** Each lane publishes `docs/lanes/<lane>/CONTRACT.md` and ships a hardcoded stub first, so no one waits and no one reaches into another lane.
3. **Continuous sync.** A hook merges `dev` into your lane branch every time you prompt your agent, and tells the agent what teammates changed. Branches never drift.
4. **Automatic PRs.** `scripts/hack ship` opens the PR into `dev` and merges it. No review queue.
5. **Issues as the message bus.** Cross-lane asks are GitHub issues; hooks put new ones in front of the owning agent within about a minute.
6. **`prod` only moves forward**, only after the demo was just performed, and every promotion is tagged for rollback.

## What's in here

```
AGENTS.md / CLAUDE.md      the rules every agent loads
.hack/lanes.json           the four lanes: owner, folders, mission; run config
scripts/hack               the one CLI (setup, run, ship, sync, inbox, request, promote, …)
.claude/settings.json      hooks: lane guard, git guard, auto-sync, inbox
.claude/skills/hack-*      wayfinder, tickets, implement, run, ship, promote, setup, init
.claude/skills/demo-judge  wow-factor / demonstrability scoring: REAL, STAGE or CUT
.claude/skills/<others>    grilling, domain-modeling, research, prototype, diagnosing-bugs, handoff (Pocock, unmodified)
.githooks/                 pre-commit lane check, pre-push prod guard
.github/workflows/         lane-check on every PR into dev
docs/                      additive, shared; docs/agents/ configures the skills
```

`scripts/hack help` lists every command.
