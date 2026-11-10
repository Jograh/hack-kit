# Hackathon Kit

Claude Code skills for a hackathon team of **2 to 6 people**.

Hackathons are lost to merge conflicts, to waiting on each other, and to demos that fall apart the moment a judge asks to try it themselves. This kit gives every person their own lane and their own chat, merges everyone's work automatically, and aims at one thing: **a working app, and a demo that shows off its best parts.**

Built on [Matt Pocock's skills](https://github.com/mattpocock/skills) (MIT). His flow is `/grill-me` → `/to-spec` → `/to-tickets` → `/implement`. This is the same flow, adapted for a team and a deadline.

## Quick start

You need [Claude Code](https://claude.com/claude-code), `git`, [`gh`](https://cli.github.com) (logged in) and `jq`. Xcode too, if you're building an iOS app.

**The lead**, before the hackathon:

```bash
gh repo create <you>/<name> --template Jograh/hack-kit --private --clone
```

Open the folder in Claude Code and run `/hack-init`. It asks for the team size, your teammates' GitHub usernames, and whether the app is iOS or something else.

**Everyone else**, once the lanes exist:

```bash
gh repo clone <you>/<name>
```

Open the folder in Claude Code and run `/hack-setup`.

## The workflow

```
Lead, before         /hack-init
Lead, at kick-off    /hack-wayfinder
Everyone, once       /hack-setup
Everyone, on repeat  /grill-me → /hack-spec → /hack-tickets → /hack-orchestrate
Optional timer       /loop 5m /hack-heartbeat      (recommended for the lead)
Team, at the end     /hack-promote
Afterwards           /hack-retro
```

Every step is a command you type into your Claude Code chat.

### 1. `/hack-init` (lead, before the hackathon)

Creates the repo's `dev` and `prod` branches, labels, auto-merge and branch protection, and invites the team. Sets the team size (2 to 6) and the kind of app: **iOS** scaffolds a SwiftUI app that runs in the simulator; **other** has the agent build a start-up script for whatever you're making.

### 2. `/hack-wayfinder` (lead, at kick-off, whole team in the room)

Turns the brief into a plan. Budget about 10% of your time. It produces:

- **The idea**, chosen from several after each is scored the way a judge would score it, including whether it can be built for real in the time you have.
- **The demo script**, `docs/DEMO.md`: the 90 seconds a judge watches, written as 5 to 8 **beats**. A beat is one step of the demo: one thing the judge sees happen, such as "tap the camera button and the photo appears in the feed". The script is a showcase, not the product: it picks the app's most impressive features and puts them where a judge can't miss them. It also lists what a judge is likely to try afterwards, off script, because that has to work too.
- **The lanes**: one per person. Each lane is a set of folders only that person's agent can write to, the beats of the demo it carries, and a contract saying what it exposes to the other lanes.
- **One stub ticket per lane**: a hardcoded version of its contract, so nobody waits on anybody.

Wayfinder decides *which* lanes exist. It does not design them. That's the next part, and each person does it for their own lane.

### 3. `/hack-setup` (everyone, once)

Puts you on your lane's branch and tells you what you own, which beats are yours, and what the other lanes expect from you. From here on, this one chat is your lane's **orchestrator**.

### 4. The loop (everyone, in their own lane)

This is where the hackathon is spent. You design your part with your orchestrator, and it builds it.

| Step | Command | What it does |
|---|---|---|
| Design | `/grill-me` | Your orchestrator interviews you about how your lane will deliver its beats (the steps of the demo that wayfinder assigned to you), until the design is clear. |
| Write it down | `/hack-spec` | Turns that conversation into one short spec issue for your lane. No interview; it writes up what you already said. |
| Split it | `/hack-tickets` | Breaks the spec into small tickets, each marked `ready-for-agent` (a worker can build it alone) or `needs-human` (it needs you). It only writes tickets for your own lane. If you need something from a teammate's lane, your orchestrator sends them a request instead, and keeps building against their stub. |
| Build it | `/hack-orchestrate` | Hands the ready tickets to background workers, up to three at once. As each finishes, your chat merges it, runs the app, checks it, and ships it to `dev`. |

While workers build, your chat shows a board and nothing else:

| Building | Ready to test | Needs you |
|---|---|---|
| #12 Camera capture | #9 Feed list: scroll it | 1. #14 Onboarding copy: A or B? |

Answer the numbered items in a word ("1: A"), try what's ready, and keep designing the next piece with `/grill-me`. Then repeat.

Two steps the orchestrator runs for you, which you can also type yourself:

- `/hack-run` builds and launches the app. In the Claude desktop app on a Mac it opens in the simulator panel next to the chat.
- `/hack-ship` commits your work, opens the PR into `dev` and merges it.

Your teammates' work doesn't wait for a ship. It is merged into your branch automatically every time you send a message, so you are always building on the latest `dev`.

Prefer to build a ticket yourself in the chat, without workers? `/hack-implement`.

### Optional: a heartbeat (recommended for the lead)

Your chat only checks for teammates' requests and new work when something happens in it: you send a message, or a worker finishes. Step away for twenty minutes and requests wait until you're back.

A heartbeat closes that gap by checking in on a timer:

```
/loop 5m /hack-heartbeat
```

| | |
|---|---|
| **Who should run it** | **The lead: recommended.** Every lane's requests for dependencies, config and wiring go to the lead, so an idle lead chat holds up the whole team. Everyone else: optional; `10m` or `15m` is plenty. |
| **What each tick does** | Pulls `dev`, reads new requests, does the small ones in your lane and replies, hands ready tickets to workers. For the lead it also wires in screens teammates just shipped and, every 30 minutes at most, performs the whole demo on `dev` and tells the owning lane if a beat broke. |
| **What it never does** | Promote, change direction, answer a question meant for you, or start anything large. Those wait under **Needs you**. |
| **What it costs** | A tick that finds nothing ends at once. A tick that finds work is a normal turn, so it uses your rate limit; lengthen the interval or stop the loop if you're running low. |

`/hack-setup` offers this to the lead when they join. To stop it, tell the chat to stop the loop.

### 5. `/hack-promote` (team, when the demo works end to end)

Performs every beat of `docs/DEMO.md`, then goes off script the way a judge would: a different input, the obvious next tap, going back, a cold relaunch. If everything holds, it moves `prod` forward and tags it. You demo from `prod`, never from `dev`; if a build goes bad, the previous tag is one checkout away.

### 6. `/hack-retro` (after the event)

Collects everything that went wrong with the kit itself and files it as issues on this repo, so the next hackathon starts better.

### Any time

| Command | Use it when |
|---|---|
| `/demo-judge` | "Is this worth building?" Verdict: **showcase** (a highlight: build it, polish it, put it in the demo), **solid** (the app needs it to really work: build it simply) or **cut**. It never says fake it. Also rehearses the demo, including what a judge will try off script. |
| `/hack-direction` | The plan changes. Shows before and after from the judge's seat; once you approve, it is recorded and sent to every teammate's agent. Anyone can do this. |
| `/hack-device` | iOS: put the app on a real iPhone over a cable. A free Apple ID is enough. Do the one-time setup the night before; see [`stacks/ios/DEVICE.md`](stacks/ios/DEVICE.md). |
| `/prototype` | A design question you can't settle by talking. |
| `/research` | You need facts: an API, what a device can do. |
| `/diagnosing-bugs` | Something is broken and the cause isn't obvious. |

You don't have to type the slash for these or for `/hack-run`, `/hack-ship` and `/hack-orchestrate`. Saying "run it" or "what if we changed the feed to a map?" reaches the same skill.

## Why it's built this way

### #1: Merge conflicts

**The problem.** Four agents writing to one repo will collide, and resolving a conflict at 3am costs more than the feature.

**The fix** is lanes. Every folder has exactly one owner, listed in `.hack/lanes.json`. Writing outside your lane is blocked three times: by a Claude hook, a git pre-commit hook, and a CI check on every PR. Shared files (config, dependencies, the app shell) belong to the lead's lane.

### #2: Waiting on each other

**The problem.** "I can't start until the API is done."

**The fix** is contracts and stubs. Each lane publishes `docs/lanes/<lane>/CONTRACT.md` and ships a hardcoded stub of it first. Need something from another lane? Your agent files a request on GitHub and keeps building against the stub; their agent sees it within a minute.

### #3: Drifting apart

**The problem.** Branches diverge, and plans change without everyone hearing.

**The fix** is that nothing is manual. A hook merges `dev` into your branch every time you prompt. Shipping opens and merges the PR on its own. Requests and changes of direction are GitHub issues that hooks push into the right agent's context.

### #4: A demo that only works on script

**The problem.** Teams build for the 90 seconds and fake the rest. Then a judge says "can I try my own?" and it falls over. Or the opposite: the app works, but six hours went into login and settings, and the impressive part is buried.

**The fix** is one rule and one skill. The rule: the app really works, and when time runs short you **cut scope, never fake it**. A smaller app that holds up beats a bigger one that only survives the script. The skill, `/demo-judge`, sorts every feature into showcase, solid or cut, so the effort goes to what judges will remember and to what they'll run into when they poke around.

What's allowed, because it's honest: sample data so the app doesn't look empty, a cached fallback in case the venue wifi dies, and hardcoded stubs while lanes are still being built. A stub is scaffolding; a beat isn't done until it runs on the real thing.

### #5: A broken demo

**The problem.** It worked an hour ago.

**The fix** is `prod`. It only moves when the whole demo has just been performed and poked at off script, and every move is tagged.

## Reference

### Skills

**You type these** (they only run when you ask):

| Skill | Who | What |
|---|---|---|
| `/hack-init` | Lead | Create the repo, set team size and kind of app |
| `/hack-wayfinder` | Lead | Brief → idea → demo script → lanes → stub tickets |
| `/hack-setup` | Everyone | Join your lane after cloning |
| `/grill-me` | Everyone | Get interviewed about a design until it's clear |
| `/hack-spec` | Everyone | Write the design down as a spec issue for your lane |
| `/hack-tickets` | Everyone | Split a spec into tickets for your lane |
| `/hack-promote` | Anyone | Move `prod` forward after performing the demo |
| `/hack-retro` | Lead | File kit improvements after the event |
| `/handoff` | Everyone | Compact the chat so a fresh one can continue |

**You type these, or the agent reaches for them** when the moment fits:

| Skill | What |
|---|---|
| `/hack-orchestrate` | Run the lane: dispatch workers, merge, test, ship |
| `/hack-heartbeat` | One timed check-in for the lane; run it with `/loop 5m /hack-heartbeat` |
| `/hack-implement` | Build one ticket in the chat, without workers |
| `/hack-run` | Build and launch the app |
| `/hack-ship` | Commit → pull `dev` → PR → merged |
| `/hack-direction` | Change the plan and tell every agent |
| `/hack-device` | iOS: install on a real iPhone |
| `/demo-judge` | Score an idea, a ticket, a change, or a rehearsal |
| `/prototype`, `/research`, `/diagnosing-bugs`, `/domain-modeling` | Unmodified from Matt Pocock |

### Commands

The skills run these for you. They also work from a terminal; `scripts/hack help` lists them all.

| Command | Does |
|---|---|
| `scripts/hack run` | Build and launch the app (`--device` for a plugged-in iPhone) |
| `scripts/hack ship "msg" --closes <n>` | Commit → pull `dev` → PR → merged |
| `scripts/hack pulse` | What changed since the last look; the check behind the heartbeat |
| `scripts/hack queue` | Your lane's tickets: ready, in progress, needs you |
| `scripts/hack request <lane> "need X"` | Ask another lane for something |
| `scripts/hack decide "what" "before / now / why"` | Record and broadcast a change of direction |
| `scripts/hack feedback "what"` | Log a problem with the kit itself |
| `scripts/hack status` | Who owns what, how far ahead or behind you are |
| `scripts/hack team <n>` | Set the team size, before lanes are named |
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
.hack/lanes.json           team size, kind of app, lanes (owner, folders, mission)
scripts/hack               the one CLI behind every skill and hook
.claude/skills/            the skills listed above
.claude/agents/            the background worker
.claude/settings.json      hooks: lane guard, git guard, auto-sync, inbox
.githooks/ .github/        the lane check, again, at commit and on every PR
stacks/ios/                Xcode project layout, simulator and real-iPhone guides
stacks/other/              how the agent builds the start-up process for any other app
docs/                      DEMO.md, decisions/, lanes/<lane>/CONTRACT.md
```
