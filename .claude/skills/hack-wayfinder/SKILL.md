---
name: hack-wayfinder
description: "LEAD, once, with the whole team in the room: turn the hackathon brief into one chosen idea, a 90-second demo script, and one lane per person (one folder + one owner each, 2 to 6 lanes) published as a map issue."
disable-model-invocation: true
---

Adapted from Matt Pocock's `wayfinder`. Same core idea (name the destination, chart a shared map on the issue tracker, leave the fog as fog) with three hackathon changes:

- **The destination is a working app and the demo that shows it off**, never a spec. The demo is the 90 seconds a judge watches; the app is what's left in their hands when they go off script afterwards. Pick an idea small enough to build for real, then plan the demo around its strongest features.
- **The map is cut into exactly one lane per person.** The team size (2 to 6) is `team` in `.hack/lanes.json`; check it with `scripts/hack team` and, if the room disagrees, fix it with `scripts/hack team <n>` before going further. Not one lane more or fewer than there are people. A lane is one person + one set of folders + one branch. Lanes are how everyone's agents work at once without a single merge conflict.
- **It is one sitting, not many sessions.** The clock is the scarcest thing. Budget ~10% of the total hackathon time for this skill, and say the budget out loud at the start.

## 1. The brief (5 min)

Read `docs/BRIEF.md` if it exists; otherwise ask for, in one round: theme/tracks and sponsor prizes · the judging criteria and their weights · hours remaining · demo format (live or video, minutes, who's on stage, whose laptop) · the four people (GitHub login, what they're fast at, Mac or not) · hard constraints (required APIs, hardware on hand). Write the answers to `docs/BRIEF.md`.

Look up facts yourself (sponsor API docs, what a device can do); only decisions go to the team.

## 2. Diverge, then judge (10 min)

Produce 6–8 ideas. Each is two lines: the pitch in one sentence, and **the moment**: what a judge is looking at 30 seconds in that makes them lean forward. No moment, no idea.

Call the Skill tool with "demo-judge" to score them. Show the table, recommend one, let the team pick. If the team already has an idea, still run it through demo-judge and say plainly what it scores low on.

## 3. Name the destination: the demo script (10 min)

Call the Skill tool with "grilling" on the winner: **two rounds maximum**, breadth-first, every question about what the judge sees and what they'll try next, never about architecture. Then write `docs/DEMO.md`:

```markdown
# Demo: <name> — <one-sentence pitch>

## The moment
<the single thing the judges will remember>

## Golden path (≈90s)
| # | Beat: what the judge sees | Lane | Why it's in the demo |
|---|---------------------------|------|----------------------|
| 1 | …                         | …    | <what this shows off> |

## Off script
<the five things a judge is most likely to try or ask after the demo ("use my own", "what if it's empty", "is that live?"), and what the app does. All of these must work.>

## Seed data
<sample content preloaded so the app doesn't look empty, and the truthful one-line answer if a judge asks. Nothing else is faked.>

## Cut list
<everything we decided NOT to build. Reopen only by editing this file.>

## Not yet specified
<fog: decisions we can't make until something is built. One line each.>
```

5–8 beats. Every beat is something visible, and every beat is a real feature of the app, chosen because it is among the most impressive things the app does. A beat that needs explaining is two beats or a cut.

## 4. Decide the stack (5 min)

Now that the idea is known, settle the shared technical choices in one pass, so four agents don't each pick their own. The kind of app (`scripts/hack stack`) was chosen at `/hack-init`; if the winning idea needs the other one, change it now.

Decide, with the team, and look the facts up yourself first (does the API have a free tier, does it work from a phone, how fast is it):

- platform and framework (already fixed for iOS: SwiftUI);
- whether there is a backend at all, and if so what and where it runs during the demo;
- each outside service or model the showcase features depend on, and the fallback if it's down;
- where data lives (on the device, a file, a hosted database) and what gets seeded;
- **the dependencies every lane will need.** List them now.

Write it to `docs/STACK.md`: one line per choice with the reason, plus the dependency list and the names (never the values) of the keys and env vars needed. Then, before the lanes start: the lead lane **installs every listed dependency and ships it**, and puts key names in `.env.example`. A dependency added up front is one request nobody has to make at hour six. Anything not on the list later is a request to the lead lane.

For a non-iOS app, also bring the start-up process in line with these choices (`stacks/other/README.md`): `scripts/dev`, `run.command`, `run.url`, `run.check`.

## 5. Cut the lanes (10 min)

Rules, in priority order:

1. **One lane per person, one owner each, disjoint folders.** Every path in the repo has exactly one owning lane. Anything not listed belongs to the lead lane.
   - **2 or 3 people:** the lead lane carries the shell *and* a feature area. Cut the demo script harder; fewer hands means fewer beats, not thinner ones.
   - **5 or 6 people:** every lane must still own something a judge sees or the demo depends on. If the beats don't split that many ways, make the extra lanes about the demo itself rather than slicing a feature in two: a **stage** lane (seed data, fallbacks for anything that depends on the network, off-script testing, the demo device) or a **pitch** lane (landing page, slides, the script and the Q&A answers). Two people in one feature folder is a merge conflict waiting to happen.
2. **Cut by folder, along the demo.** Prefer lanes that each own whole beats (a screen, a service, the data/AI behind a beat) over layers ("frontend"/"backend") that force every beat through two people.
3. **One lane is the lead lane**: it owns the app shell / composition root, root config, dependency manifests, `docs/DEMO.md`, and integration. Give it the lightest feature load.
4. **Each lane publishes a contract and a stub.** `docs/lanes/<lane>/CONTRACT.md` says what the lane exposes (function/endpoint/view names, data shapes, example values) and what it consumes. Each lane's *first* ticket is a hardcoded stub of that contract, shipped within 30 minutes, so nobody ever waits on anybody. The stub is scaffolding: the lane replaces it with the real thing, and a beat isn't done while it still runs on one.
5. **Balance by risk, not by size.** The lane carrying *the moment* goes to the fastest person and gets the least else.
6. **Stack-specific conflict traps** are designed out here: see the stack's guide: `stacks/ios/PROJECT.md` for Xcode projects, `stacks/other/README.md` for anything else.

Present the cut as a table (lane · owner · folders · beats carried · exposes · consumes) and get a yes from the team.

## 6. Write it down and publish

1. Rewrite `.hack/lanes.json`: real lane names (short, lowercase, one word), `owner` = GitHub username, `mission`, `paths` (folder prefixes ending in `/`), `lead`, and `run` if the stack is known.
2. Create each lane's folder(s) with a one-line `README.md`, and `docs/lanes/<lane>/CONTRACT.md`.
3. Run `scripts/hack labels`.
4. Publish **the map**: one issue labelled `hack:map` and `broadcast`, titled `MAP: <project>`, whose body is: the destination (link `docs/DEMO.md`, quote *the moment*), the lane table, **Decisions so far** (one line each), **Not yet specified**, **Cut list**. It is an index: it links, it does not restate.
5. Call the Skill tool with "hack-tickets" in **kick-off mode**: it publishes exactly one ticket per lane, the stub of that lane's contract. Nothing more. Wayfinder decides *which* lanes exist and what each must expose; **how** a lane delivers its beats is designed by its owner, in their own chat (`/grill-me` → `/hack-spec` → `/hack-tickets`).
6. Ship it: `scripts/hack setup <lead lane>` (puts you on your lane branch, keeping these uncommitted files), then `scripts/hack ship "Chart the map"`. The lead lane is allowed to add new files in any lane's folder, which is what makes this scaffold legal; it still can't edit another lane's existing files.
7. Tell the team, verbatim: **clone the repo, open it in Claude Code, run `/hack-setup`. Then design your lane with `/grill-me`.**

## Re-charting mid-hackathon

Invoked again with a map already present, do not start over. Read the map and `docs/DEMO.md`, ask what changed, then: move beats to the Cut list, graduate fog into tickets, or re-balance lanes. Changing a lane's folders mid-flight is the one thing to avoid; move *tickets*, not folders. Post what changed as a comment on the map issue: it carries the `broadcast` label, so every teammate's agent reads it within a minute.
