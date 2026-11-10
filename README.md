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
- To put an iOS app on a real iPhone: a cable and any Apple ID. A free one is enough; nobody needs a paid developer account.

## Begin

The examples below follow a made-up team of four building **Recall**, an iOS app that remembers what you photograph. They show the shape of what you'll see, not exact output.

### 1. Lead, before the hackathon (10 minutes)

Create your team's repo from this template:

```bash
gh repo create <you>/<name> --template Jograh/hack-kit --private --clone
```

Open the folder in Claude Code and type:

```
/hack-init
```

It asks for the **team size (2 to 6)**, the repo name and your teammates' GitHub usernames, then sets up the `dev` and `prod` branches, labels, auto-merge and branch protection, and invites everyone.

It also asks which **kind of app** you're building:

| Choice | What happens |
|---|---|
| **iOS** | Scaffolds an empty SwiftUI app whose Xcode project never conflicts, and confirms it opens in the simulator. Everything iOS-specific lives in [`stacks/ios/`](stacks/ios/README.md). |
| **Other** | You say what it's made of ("FastAPI + Vite"), and the agent builds the start-up process for it, so "run it" brings up the servers and opens the page. See [`stacks/other/`](stacks/other/README.md). |

Team size can be changed until the lanes are named: `scripts/hack team <n>`.

<details open>
<summary><b>In practice</b></summary>

> **You:** /hack-init
>
> **Claude:** How many people are on the team, including you (2 to 6)? What should the repo be called, public or private, and what are your teammates' GitHub usernames?
>
> **You:** 4 of us. acme/recall, private. maya-k, dsantos, priya-r
>
> **Claude:** Creating **acme/recall** (private) with four lanes and inviting maya-k, dsantos and priya-r. Go ahead?
>
> **You:** yes
>
> **Claude:** Repo ready: `dev` is the default branch, `prod` is the demo branch, and PRs into `dev` must pass the lane check. Your three teammates need to accept the invite. Is this an iOS app or something else?
>
> **You:** iOS, call it Recall. We'll demo on my phone.
>
> **Claude:** Recall is scaffolded and opens in the simulator. For the phone there's a one-time setup; first step: open Xcode → Settings → Accounts and sign in with your Apple ID. Tell me when that's done.

</details>

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

<details open>
<summary><b>In practice</b></summary>

The demo script, `docs/DEMO.md`:

| # | What the judge sees | Lane | Real or staged |
|---|---|---|---|
| 1 | Point the phone at a whiteboard, tap once | capture | real |
| 2 | The photo lands in the feed, already titled and tagged | feed | staged: tags are seeded |
| 3 | Ask "what did we decide on Tuesday?" and get the answer, with the photo | brain | real: this is the moment |

The lanes, in `.hack/lanes.json`:

| Lane | Owner | Folders | Exposes to the others |
|---|---|---|---|
| shell (lead) | you | `Recall/App/`, `Recall/Shared/` | the app shell, shared types |
| capture | maya-k | `Recall/Capture/` | `CaptureView`, a saved `Photo` |
| feed | dsantos | `Recall/Feed/` | `FeedView`, the list of items |
| brain | priya-r | `Recall/Brain/` | `ask(question) -> Answer` |

</details>

### 3. Every teammate, once (2 minutes)

```bash
gh repo clone <org>/<repo> && cd <repo>
```

Open the folder in Claude Code and type:

```
/hack-setup
```

You're on your lane's branch, your agent knows what it owns, and it briefs you on your first tickets.

<details open>
<summary><b>In practice</b></summary>

> **maya-k:** /hack-setup
>
> **Claude:** You're the **capture** lane, on branch `lane/capture`.
>
> - **You own:** `Recall/Capture/` and `docs/lanes/capture/`. I can't write anywhere else.
> - **Your beat:** #1, point the phone at a whiteboard and tap once.
> - **You expose:** `CaptureView` and a saved `Photo`, used by feed and brain.
> - **Your tickets:** #7 Stub the capture contract · #12 Camera capture · #13 Share sheet · #14 Onboarding copy (needs you)
>
> This chat runs your lane: background workers build the ready tickets, and anything that needs you shows up under **Needs you**. Start?
>
> **maya-k:** go

</details>

## During the hackathon

You talk to one chat. These are the things you say to it.

### "Work the queue"

Background workers build your `ready-for-agent` tickets, up to three at a time. When one finishes, your chat merges it, runs it, checks it and ships it. You get a board:

| Building | Ready to test | Needs you |
|---|---|---|
| #12 Camera capture | #9 Feed list: scroll it | 1. #14 Onboarding copy: A or B? |
| #13 Share sheet | | 2. Promote to prod? |

Answer the numbered items in a word ("1: A"). Keep brainstorming in the same chat while workers run.

<details open>
<summary><b>In practice</b></summary>

> **maya-k:** work the queue
>
> **Claude:**
>
> | Building | Ready to test | Needs you |
> |---|---|---|
> | #7 Capture stub | | 1. #14 Onboarding copy: A "Snap it, find it" or B "Never lose a whiteboard"? |
> | #12 Camera capture | | |
>
> **maya-k:** 1: B. Also, could the shutter button pulse while it saves?
>
> **Claude:** Noted B on #14. A pulsing shutter is polish a judge would notice, so I've written it up as #19 and queued it behind the camera.
>
> *(a few minutes later)*
>
> **Claude:** Shipped #7.
>
> | Building | Ready to test | Needs you |
> |---|---|---|
> | #19 Shutter pulse | #12 Camera capture: tap the shutter, the photo should appear as a thumbnail | |

</details>

### "Run it"

Builds and launches the app. In the Claude desktop app it appears in the simulator panel next to the chat, and you tap it yourself. The orchestrator also launches it on its own when something visible lands, and tells you what to try.

<details open>
<summary><b>In practice</b></summary>

> **maya-k:** run it
>
> **Claude:** Running on iPhone 17 in the panel. I tapped the shutter and the thumbnail appeared. Try it yourself, then tap the thumbnail: the share sheet isn't wired yet, so nothing should happen.

</details>

### "Put it on my phone" (iOS)

Plug the iPhone in with a cable, unlock it, and say so. The app is built, installed and opened on the phone.

The first time, there is a one-time setup of about ten minutes, and your chat walks you through it one step at a time: sign in to Xcode with an Apple ID, trust the Mac, turn on Developer Mode, trust your own certificate on the phone. Do it the night before. The full guide is [`stacks/ios/DEVICE.md`](stacks/ios/DEVICE.md).

Signing details stay on your Mac and are never committed, so nobody's Apple account ends up in the project file. Your agent can't see a real phone, so it checks its own work in the simulator and asks you what the phone shows.

<details open>
<summary><b>In practice</b></summary>

> **You:** put it on my phone
>
> **Claude:** Two things are missing: Developer Mode is off. On the phone, open Settings → Privacy & Security → Developer Mode, switch it on and restart. Tell me when it's back.
>
> **You:** done
>
> **Claude:** Installed on "Recall Demo Phone". iOS will say Untrusted Developer the first time: Settings → General → VPN & Device Management → your Apple ID → Trust. Then I'll launch it again.
>
> **You:** ok trusted
>
> **Claude:** Running on the phone. Does Home show the camera button and an empty feed?

</details>

### "I need X from the camera lane"

Your agent files a request on GitHub and keeps building against a stub of that lane's contract. The other lane's agent sees the request within a minute. You never wait on each other.

<details open>
<summary><b>In practice</b></summary>

> **dsantos:** the feed needs a timestamp on every photo
>
> **Claude:** `Photo` belongs to the capture lane, so I've asked them for a `takenAt` date. Until they answer I'm building the feed against a hardcoded one.
>
> Meanwhile, in maya-k's chat:
>
> **Claude:** The feed lane asked for a `takenAt` date on `Photo`. It's a one-line addition in our lane, so I've added it, updated our contract and replied on the request.

</details>

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
| Right after a promote (iOS) | "put the demo build on the phone" | Builds from a clean copy of `prod` and installs it on the demo iPhone |
| On stage | | Demo from `prod`, never from `dev`. A bad build? Check out the previous `demo-*` tag. Keep the simulator ready on the Mac as the fallback. |

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
| `hack-device` | Anyone with a Mac and an iPhone | "Put it on my phone": one-time setup, then build, install, launch |
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
| `scripts/hack run --device` | iOS: build, install and launch on the plugged-in iPhone (`--prod` for the promoted build) |
| `scripts/hack device` | iOS: the one-time phone checklist |
| `scripts/hack stack ios\|other` | Choose the kind of app |
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
.hack/lanes.json           team size, stack, lanes (owner, folders, mission), run config
scripts/hack               the one CLI behind every skill and hook
.claude/settings.json      hooks: lane guard, git guard, auto-sync, inbox
.claude/agents/            the background worker
.claude/skills/            the skills listed above
.githooks/                 pre-commit lane check, pre-push prod guard
.github/workflows/         lane check on every PR into dev
stacks/ios/                the Xcode project layout, simulator, and real-iPhone guides
stacks/other/              how the agent builds the start-up process for any other app
docs/                      DEMO.md, decisions/, lanes/<lane>/CONTRACT.md; additive and shared
```
