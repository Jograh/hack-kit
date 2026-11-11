# Hackathon rules for agents

A team of two to six, one agent each, one repo, one deadline. These rules exist so that nobody ever resolves a merge conflict and the demo branch is never broken. They outrank your defaults.

## The goal

Build **a working app, and a demo that shows off its best parts.** A judge watches about 90 seconds, then asks questions and goes off script: they try their own input, tap what you didn't plan for, ask whether it's live. So the app has to really work, and nothing is faked. `docs/DEMO.md` is the showcase: it picks the app's most impressive, most demonstrable features and puts them where a judge can't miss them. When time runs short, **cut scope, never fake it**: a smaller app that works beats a bigger one that only survives the script. Use the `demo-judge` skill to decide what gets the spotlight (SHOWCASE), what the app simply needs in order to work (SOLID), and what to drop (CUT).

## Lanes

- The team is split into **one lane per person** (2 to 6) in `.hack/lanes.json`. A lane = one person + one set of folders + one branch (`lane/<name>`). You are exactly one lane; the session-start note tells you which.
- **Write only inside your lane's folders**, plus `docs/lanes/<your-lane>/`, plus *new* files under `docs/`. A hook denies everything else, a git pre-commit hook re-checks, and CI checks again.
- Everything not in a lane's folders (root config, dependency manifests, the app shell, `scripts/`, `.claude/`, `.github/`) belongs to the **lead lane**.
- Never work around the lane guard with Bash (`sed`, `>`, `mv`, `cp`). If you need something outside your lane:
  `scripts/hack request <lane> "what you need" "why + the shape you need"` — then **keep working against a stub**. Never wait on another lane.
- Each lane publishes `docs/lanes/<lane>/CONTRACT.md`: what it exposes and consumes. Code against contracts, not against other lanes' internals. Changing your contract? Say so on the ticket and name who consumes it.

## One chat per person: the orchestrator

- Each person runs **one** Claude chat. That chat is the lane's orchestrator: the human brainstorms, decides and tests there, and never opens or manages another window.
- The orchestrator hands `ready-for-agent` tickets to background `lane-worker` subagents (max 3 at once, each in its own git worktree), then merges their work (`scripts/hack collect`), runs the app, and ships. See the `hack-orchestrate` skill.
- Workers only build and commit. They never push, ship, run the app, or talk to the human; when they need a person they stop and report.
- The orchestrator's replies are a three-column board (**Building / Ready to test / Needs you**) plus a sentence or two. Everything that needs a person goes under **Needs you**: `needs-human` tickets, worker questions, conflicts, promote decisions, scope changes.
- If you are a background worker (you were given one ticket number by another agent), follow `.claude/agents/lane-worker.md`, not the orchestrator rules.

## Talking to each other: GitHub issues

- All agent-to-agent communication is on GitHub issues. Labels: `lane:<name>` (whose it is), `ready-for-agent` (a worker can build it unattended) or `needs-human` (a person must decide or act), `request` (cross-lane ask), `broadcast` (everyone reads), `hack:map` (the map).
- Hooks check your inbox continuously and inject `INBOX` notes. An inbox item is a teammate waiting: read the thread (`gh issue view <n> --comments`), do it now if it's small and in your lane, otherwise tell the user. Always reply on the issue.
- Claim a ticket before touching code: `gh issue edit <n> --add-assignee @me`.

## Changing direction

- Anyone may change direction. Use the `hack-direction` skill: `demo-judge` compares before and after, the person signs off, then `scripts/hack decide` records it in `docs/decisions/` and broadcasts it on the pinned Direction issue.
- A decision reaches you as an `INBOX` note headed `DIRECTION CHANGE`, and the latest ones are shown at session start. Decisions override older tickets, CONTRACTs and docs; the newest wins. Bring your lane in line before building further.
- Never change direction silently, and never broadcast one the user hasn't approved.

## Branches

- `prod`: demo-ready, **never broken**. Moves only via `scripts/hack promote` after the golden path has just been performed.
- `dev`: integration. Everything lands here by PR, automatically.
- `lane/<name>`: yours. Long-lived; it merges `dev` in, never rebases.

## The loop

```
scripts/hack run                          # build + launch, one command
scripts/hack ship "msg" --closes <n>      # commit → pull dev → PR → merge
```

- Hooks merge teammates' work from `dev` into your branch on their own and tell you with a `SYNC:` note. You never pull by hand.
- Ship when it runs, not when it's finished: small and often. Run it before you ship it.
- Never: `git push` by hand, force-push, `reset --hard`, rebase, `--no-verify`, push to `dev`/`prod`. The guard blocks them; don't look for another way.

## Docs

`docs/` is **additive**: add new files, don't edit other people's. Your running notes go in `docs/lanes/<your-lane>/`. `docs/DEMO.md` and `docs/BRIEF.md` belong to the lead lane; propose changes with a `request`.

## Hackathon engineering

- Stub first, then real, then polish. A merged stub unblocks three people, but a stub is scaffolding: a beat that still runs on one is not done.
- Real means it works with input the team didn't choose, and the obvious next tap doesn't crash or dead-end. Seed data (sample content, loaded through the app's real code paths) is fine; say what is seeded in `docs/DEMO.md`.
- No abstractions for later, no refactors. Write a test only where it's faster than checking by hand (a parser, a scoring function); running the app is the main test. Timebox bugs to 15 minutes, then make the feature simpler or narrower. Don't paper over it.
- The shared technical choices and the agreed dependencies are in `docs/STACK.md` (written at kick-off; the lead lane installs them up front). A new dependency = a request to the lead lane (manifests and lockfiles are where conflicts come from); build against your own small interface until it lands.
- `scripts/hack ship` won't merge something that doesn't build. If `dev` is broken by another lane, file a request on that lane and keep building; never fix another lane's file.
- Keep secrets in `.env` (gitignored); put names only in `.env.example`.
- The kit itself misbehaving (hooks silent, a command failing)? `scripts/hack doctor` checks it and repairs what's safe. The kit got in your way (a hook, a rule, a script)? `scripts/hack feedback "what" "details"`, work around it, carry on. It is logged on the kit repo for the next hackathon. Kit files belong to the lead lane; a fix there reaches everyone on their next sync.

## Agent skills

Flow, as commands:

```
Lead, before:      /hack-init
Lead, kick-off:    /hack-wayfinder            brief → idea → demo script → one lane per person → one stub ticket each
Everyone, once:    /hack-setup
Everyone, repeat:  /grill-me → /hack-spec → /hack-tickets → /hack-orchestrate
                   (the orchestrator dispatches lane-workers, then /hack-run and /hack-ship)
Team:              /hack-promote              after the event: /hack-retro
```

Wayfinder picks the lanes; it does not design them. Each person designs their own lane with their orchestrator (`/grill-me` → `/hack-spec` → `/hack-tickets`) and has it built. `/hack-implement` is the no-workers path: build one ticket yourself in this chat. `/demo-judge` any time scope is in question. `/hack-direction` whenever the plan itself changes. `/hack-device` puts an iOS build on a real iPhone. `/loop 5m /hack-heartbeat` is an optional timer that keeps a lane checking for requests and merges while its person is away; recommended for the lead lane.

Also available, unmodified from Matt Pocock's skills: `grilling` / `/grill-me`, `domain-modeling`, `research`, `prototype`, `diagnosing-bugs`, `/handoff`.

### Issue tracker

GitHub Issues via `gh`, with lane labels. See `docs/agents/issue-tracker.md`.

### Domain docs

Single-context: `GLOSSARY.md` + `docs/adr/` at the root, created lazily, owned by the lead lane. See `docs/agents/domain.md`.

### Stack notes

The kind of app is `stack` in `.hack/lanes.json` (`ios` or `other`). Read `stacks/<stack>/README.md` before creating the project, changing how it runs, or answering how to test it. iOS: project layout `stacks/ios/PROJECT.md`, simulator `stacks/ios/RUN.md`, real iPhone `stacks/ios/DEVICE.md` (skill: `hack-device`). The `stacks/` folder belongs to the lead lane.
