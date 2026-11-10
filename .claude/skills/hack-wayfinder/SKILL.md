---
name: hack-wayfinder
description: "LEAD, once, with the whole team in the room: turn the hackathon brief into one chosen idea, a 90-second demo script, and exactly four lanes (one folder + one person each) published as a map issue."
disable-model-invocation: true
---

Adapted from Matt Pocock's `wayfinder`. Same core idea (name the destination, chart a shared map on the issue tracker, leave the fog as fog) with three hackathon changes:

- **The destination is always a demo**, never a spec: the 90 seconds a judge watches. Everything is planned backwards from it.
- **The map is cut into exactly four lanes.** Not three, not five. A lane is one person + one set of folders + one branch. Lanes are how four agents work at once without a single merge conflict.
- **It is one sitting, not many sessions.** The clock is the scarcest thing. Budget ~10% of the total hackathon time for this skill, and say the budget out loud at the start.

## 1. The brief (5 min)

Read `docs/BRIEF.md` if it exists; otherwise ask for, in one round: theme/tracks and sponsor prizes · the judging criteria and their weights · hours remaining · demo format (live or video, minutes, who's on stage, whose laptop) · the four people (GitHub login, what they're fast at, Mac or not) · hard constraints (required APIs, hardware on hand). Write the answers to `docs/BRIEF.md`.

Look up facts yourself (sponsor API docs, what a device can do); only decisions go to the team.

## 2. Diverge, then judge (10 min)

Produce 6–8 ideas. Each is two lines: the pitch in one sentence, and **the moment**: what a judge is looking at 30 seconds in that makes them lean forward. No moment, no idea.

Call the Skill tool with "demo-judge" to score them. Show the table, recommend one, let the team pick. If the team already has an idea, still run it through demo-judge and say plainly what it scores low on.

## 3. Name the destination: the demo script (10 min)

Call the Skill tool with "grilling" on the winner: **two rounds maximum**, breadth-first, every question about what the judge sees, never about architecture. Then write `docs/DEMO.md`:

```markdown
# Demo: <name> — <one-sentence pitch>

## The moment
<the single thing the judges will remember>

## Golden path (≈90s)
| # | Beat: what the judge sees | Lane | Real or staged |
|---|---------------------------|------|----------------|
| 1 | …                         | …    | real / staged: <how> |

## Staged honestly
<what is hardcoded/mocked for the demo, and the one-line truthful answer if a judge asks>

## Cut list
<everything we decided NOT to build. Reopen only by editing this file.>

## Not yet specified
<fog: decisions we can't make until something is built. One line each.>
```

5–8 beats. Every beat is something visible. A beat that needs explaining is two beats or a cut.

## 4. Cut the four lanes (10 min)

Rules, in priority order:

1. **Exactly four lanes, one owner each, disjoint folders.** Every path in the repo has exactly one owning lane. Anything not listed belongs to the lead lane.
2. **Cut by folder, along the demo.** Prefer lanes that each own whole beats (a screen, a service, the data/AI behind a beat) over layers ("frontend"/"backend") that force every beat through two people.
3. **One lane is the lead lane**: it owns the app shell / composition root, root config, dependency manifests, `docs/DEMO.md`, and integration. Give it the lightest feature load.
4. **Each lane publishes a contract and a stub.** `docs/lanes/<lane>/CONTRACT.md` says what the lane exposes (function/endpoint/view names, data shapes, example values) and what it consumes. Each lane's *first* ticket is a hardcoded stub of that contract, shipped within 30 minutes, so nobody ever waits on anybody.
5. **Balance by risk, not by size.** The lane carrying *the moment* goes to the fastest person and gets the least else.
6. **Stack-specific conflict traps** are designed out here: see `docs/agents/stack-ios.md` for Xcode projects.

Present the cut as a table (lane · owner · folders · beats carried · exposes · consumes) and get a yes from the team.

## 5. Write it down and publish

1. Rewrite `.hack/lanes.json`: real lane names (short, lowercase, one word), `owner` = GitHub login, `mission`, `paths` (folder prefixes ending in `/`), `lead`, and `run` if the stack is known.
2. Create each lane's folder(s) with a one-line `README.md`, and `docs/lanes/<lane>/CONTRACT.md`.
3. Run `scripts/hack labels`.
4. Publish **the map**: one issue labelled `hack:map` and `broadcast`, titled `MAP: <project>`, whose body is: the destination (link `docs/DEMO.md`, quote *the moment*), the lane table, **Decisions so far** (one line each), **Not yet specified**, **Cut list**. It is an index: it links, it does not restate.
5. Call the Skill tool with "hack-tickets" to publish each lane's first tickets under the map.
6. Ship it: `scripts/hack setup <lead lane>` (puts you on your lane branch, keeping these uncommitted files), then `scripts/hack ship "Chart the map"`. The lead lane is allowed to add new files in any lane's folder, which is what makes this scaffold legal; it still can't edit another lane's existing files.
7. Tell the team, verbatim: **clone the repo, open it in Claude Code, run `/hack-setup`.**

## Re-charting mid-hackathon

Invoked again with a map already present, do not start over. Read the map and `docs/DEMO.md`, ask what changed, then: move beats to the Cut list, graduate fog into tickets, or re-balance lanes. Changing a lane's folders mid-flight is the one thing to avoid; move *tickets*, not folders. Post what changed as a comment on the map issue: it carries the `broadcast` label, so every teammate's agent reads it within a minute.
