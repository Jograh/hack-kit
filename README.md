# Hackathon Kit

A Claude Code setup for a hackathon team of **2 to 6 people**. Everyone gets one chat that builds, tests and ships their part; nobody resolves a merge conflict; and every decision is made from the judge's seat.

Built on [Matt Pocock's skills](https://github.com/mattpocock/skills) (MIT), adapted for a deadline.

- [What you get](#what-you-get)
- [What you need](#what-you-need)
- [Begin](#begin)
- [During the hackathon](#during-the-hackathon)
- [Demo time](#demo-time)
- [After](#after)
- [How it prevents merge conflicts](#how-it-prevents-merge-conflicts)
- [Reference](#reference)

## What you get

| | |
|---|---|
| **One lane per person** | Every folder has exactly one owner. Your agent physically can't write outside yours. |
| **One chat per person** | Your chat is your lane's orchestrator. It hands tickets to background workers, merges their work, runs the app and ships. You never manage a second window. |
| **A board, not a wall of text** | Replies are three columns: Building, Ready to test, Needs you. |
| **One-step run** | "Run it" builds and launches the app in Claude's simulator panel, beside the chat. |
| **Automatic everything else** | PRs open and merge on their own. Teammates' work is pulled into your branch every time you prompt. New issues for your lane reach your agent within about a minute. |
| **A judge in the loop** | Ideas, tickets and changes of direction are scored on wow and demonstrability, and sorted into build it for real, stage it, or cut it. |
| **A demo branch that is never broken** | `prod` only moves after the demo has just been performed, and every move is tagged for rollback. |

## What you need

- [Claude Code](https://claude.com/claude-code). The desktop app on a Mac gives you the built-in iOS simulator panel; the terminal works for everything else.
- `git`, [`gh`](https://cli.github.com) (logged in: `gh auth login`) and `jq`.
- Xcode, only if you are building an iOS app and want to run it. No Mac means no local testing: you ship, and a teammate with a Mac runs it.

## Begin

### 1. Lead, before the hackathon (10 minutes)

Create your team's repo from this template:

```bash
gh repo create <you>/<name> --template Jograh/hack-kit --private --clone
```

Open the folder in Claude Code and type:

```
/hack-init
```

It asks for the **team size (2 to 6)**, the repo name and your teammates' GitHub logins, then sets up the `dev` and `prod` branches, labels, auto-merge and branch protection, and invites everyone. If you already know the stack, it scaffolds an empty app so day one starts at the idea.

Team size can be changed until the lanes are named: `scripts/hack team <n>`.

### 2. Kick-off, whole team in the room (about 10% of your time)

In the lead's chat:

```
/hack-wayfinder
```

| Step | What comes out |
|---|---|
| The brief | Theme, judging criteria, hours left, demo format |
| Ideas | Each scored for wow and demonstrability; one is chosen |
| The demo script | `docs/DEMO.md`: the 90 seconds a judge watches. This is the only definition of done. |
| The lanes | One per person, each with its folders, its beats of the demo, and a contract saying what it exposes to the others |
| Tickets | One lane each, marked either `ready-for-agent` (a worker can build it alone) or `needs-human` |

### 3. Every teammate, once (2 minutes)

```bash
gh repo clone <org>/<repo> && cd <repo>
```

Open the folder in Claude Code and type:

```
/hack-setup
```

You're on your lane's branch, your agent knows what it owns, and it briefs you on your first tickets.

## During the hackathon

You talk to one chat. These are the things you say to it.

### "Work the queue"

Background workers build your `ready-for-agent` tickets, up to three at a time. When one finishes, your chat merges it, runs it, checks it and ships it. You get a board:

| Building | Ready to test | Needs you |
|---|---|---|
| #12 Camera capture | #9 Feed list: scroll it | 1. #14 Onboarding copy: A or B? |
| #13 Share sheet | | 2. Promote to prod? |

Answer the numbered items in a word ("1: A"). Keep brainstorming in the same chat while workers run.

### "Run it"

Builds and launches the app. In the Claude desktop app it appears in the simulator panel next to the chat, and you tap it yourself. The orchestrator also launches it on its own when something visible lands, and tells you what to try.

### "I need X from the camera lane"

Your agent files a request on GitHub and keeps building against a stub of that lane's contract. The other lane's agent sees the request within a minute. You never wait on each other.

### "What if we changed…?"

Anyone can change direction. Before anything is sent, you get a before-and-after from the judge's seat:

```
Before  Feed is a list of saved items           41/60
After   Feed is a map with pins                 47/60
Gains:  a visible moment in the first 20 seconds
Costs:  two feed tickets thrown away; brain lane's contract changes
Verdict: SWITCH, BUT STAGE the pin data
```

Approve the wording and it is recorded in `docs/decisions/` and pushed to every teammate's agent, which brings its own lane in line.

### "Is this worth building?"

The `demo-judge` skill answers one question: does a judge see it in the 90 seconds? The verdict is **real** (build it properly), **stage** (hardcode, seed or replay it) or **cut**.

### When the kit gets in your way

Agents log it with `scripts/hack feedback` and work around it. Nobody stops to fix the kit mid-event.

## Demo time

| When | Say | What happens |
|---|---|---|
| The golden path works end to end | `/hack-promote` | Performs every beat of `docs/DEMO.md`; if all pass, moves `prod` forward and tags it |
| Before you present | "rehearse the demo" | Time to wow, dead air, single points of failure and their fallbacks, likely judge questions |
| On stage | | Demo from `prod`, never from `dev`. A bad build? Check out the previous `demo-*` tag. |

## After

```
/hack-retro
```

Collects everything the agents logged about the kit, plus your own gripes, merges duplicates, and files clean issues on the kit's repo so the next hackathon starts better.

## How it prevents merge conflicts

1. **Lanes.** `.hack/lanes.json` gives every folder exactly one owner. Writing elsewhere is blocked three times: by a Claude hook, a git pre-commit hook, and a CI check on every PR.
2. **Contracts and stubs.** Each lane publishes `docs/lanes/<lane>/CONTRACT.md` and ships a hardcoded stub of it first, so nobody waits and nobody reaches into another lane.
3. **Continuous sync.** A hook merges `dev` into your lane branch every time you prompt, and tells your agent what changed. Branches never drift.
4. **Automatic PRs.** Shipping opens the PR into `dev` and merges it. There is no review queue.
5. **Issues as the message bus.** Requests, decisions and broadcasts are GitHub issues, pushed into the right agent's context by hooks.
6. **Additive docs.** Anyone may add a file under `docs/`; nobody edits someone else's.
7. **`prod` only moves forward**, only after the demo was just performed.

## Reference

### Skills

| Skill | Who | When |
|---|---|---|
| `/hack-init` | Lead | Once, before: create the repo, set the team size |
| `/hack-wayfinder` | Lead | Kick-off: brief → idea → demo script → lanes |
| `/hack-tickets` | Lead | Break the demo script into one-lane tickets |
| `/hack-setup` | Everyone | Once, after cloning |
| `hack-orchestrate` | Everyone | "Work the queue": the main loop |
| `hack-implement` | Everyone | Build one ticket in the chat, without workers |
| `hack-run` | Everyone | "Run it" |
| `hack-ship` | Everyone | "Ship it" (the orchestrator does this for you) |
| `hack-direction` | Everyone | A change of plan |
| `demo-judge` | Everyone | Score an idea, a ticket, a change, or a rehearsal |
| `/hack-promote` | Anyone | Move `prod` forward |
| `/hack-retro` | Lead | After the event |

Also included, unmodified from Matt Pocock: `grilling` / `/grill-me`, `domain-modeling`, `research`, `prototype`, `diagnosing-bugs`, `/handoff`.

### Commands

Your agent runs these for you; they also work from a terminal. `scripts/hack help` lists them all.

| Command | Does |
|---|---|
| `scripts/hack run` | Build and launch the app |
| `scripts/hack ship "msg" --closes <n>` | Commit → pull `dev` → PR → merged |
| `scripts/hack queue` | Your lane's tickets: ready, in progress, needs you |
| `scripts/hack request <lane> "need X"` | Ask another lane for something |
| `scripts/hack decide "what" "before / now / why"` | Record and broadcast a direction change |
| `scripts/hack direction` | The latest decisions |
| `scripts/hack feedback "what"` | Log a problem with the kit itself |
| `scripts/hack status` | Who owns what, how far ahead or behind you are |
| `scripts/hack team <n>` | Set the team size (2 to 6), before lanes are named |
| `scripts/hack promote` | `dev` → `prod`, tagged |

### Branches

| Branch | Rule |
|---|---|
| `prod` | Demo-ready, never broken. Moves only through promote. |
| `dev` | Integration. Everything lands here by PR, automatically. |
| `lane/<name>` | Yours. Long-lived; merges `dev` in, never rebases. |

### What's in the repo

```
AGENTS.md / CLAUDE.md      the rules every agent loads
.hack/lanes.json           team size, lanes (owner, folders, mission), run config
scripts/hack               the one CLI behind every skill and hook
.claude/settings.json      hooks: lane guard, git guard, auto-sync, inbox
.claude/agents/            the background worker
.claude/skills/            the skills listed above
.githooks/                 pre-commit lane check, pre-push prod guard
.github/workflows/         lane check on every PR into dev
docs/                      DEMO.md, decisions/, lanes/<lane>/CONTRACT.md; additive and shared
```
