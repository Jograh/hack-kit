---
name: demo-judge
description: Look at an idea, a ticket, or a demo script the way a hackathon judge will experience it. Use when choosing between ideas, deciding whether a feature belongs in the demo, is needed for the app to work, or should be cut, when someone proposes extra scope or a change of direction (before vs after), or before a demo rehearsal.
---

A judge gives you about three minutes and has seen thirty other projects. They watch the demo, then they **ask questions and go off script**: "can I try my own?", "what happens if I tap that?", "is this live?". So two things have to be true:

1. **The app really works.** Everything shown works with input the team didn't choose, and the obvious next tap doesn't dead-end or crash. Nothing is faked.
2. **The demo shows off its best parts.** The demo script doesn't define the product; it picks the app's most impressive, most demonstrable features and puts them where a judge can't miss them, so they leave with something to think about.

This skill makes decisions from that seat. It decides **what gets the spotlight and what gets cut**. It never decides to fake something.

Read `docs/BRIEF.md` (judging criteria, hours left) and `docs/DEMO.md` if they exist. If the brief has the real criteria and weights, score against those too and let them override the default weights below.

## Mode 1: ideas

Score each idea 1–5 per row; total = Σ score × weight (max 60).

| Criterion | ×  | 5 looks like | 1 looks like |
|-----------|----|--------------|--------------|
| **Wow moment** | 3 | One visible instant that gets a reaction, in the first 30 seconds | Value appears only after explanation |
| **Demonstrability** | 3 | Runs live, and a judge could take the device and drive it with their own input | Needs slides, a backstory, or "imagine that…" |
| **Criteria fit** | 2 | Hits the top-weighted judging criteria and a sponsor prize | Good project, wrong hackathon |
| **Buildable for real** | 2 | The whole team in parallel reaches a genuinely working app with half the time left | Only a scripted path could be finished; the core would have to be faked |
| **Story** | 1 | Problem → magic → payoff fits one sentence | Needs a paragraph |
| **Stage safety** | 1 | Survives bad wifi, a cold start, a nervous presenter | Depends on a live third party behaving, with no fallback |

Output one table (ideas × criteria, totals), then for the top two: *the moment* in one line, the biggest risk, and the hardest part to make real in the time available. Recommend one. Be blunt about a favourite that scores badly, and about an idea whose moment can't be built for real: shrink the idea until it can.

## Mode 2: a ticket or a scope proposal

Give exactly one verdict:

- **SHOWCASE**: this is one of the things a judge should remember. Build it for real, put it in the demo script, and spend the polish here (speed, animation, copy).
- **SOLID**: it isn't a highlight, but the app doesn't truly work without it, or a judge who pokes around will run into it (the screen behind the obvious next tap, an empty state, a failed network call, input other than the demo's). Build it for real, as simply as possible. No polish.
- **CUT**: the app works and the demo lands without it (accounts and login, settings, a second platform, scale, admin tools, features nobody will reach in three minutes). Add it to the Cut list in `docs/DEMO.md`.

How to choose when time is short: **cut scope, never fake it.** A smaller app that really works beats a bigger one that only survives the script. If a showcase feature can't be made real in time, make it narrower (fewer cases, one platform, one language) until it can.

What is allowed, because it is honest:

- **Seed data**: sample content so the app doesn't look empty (photos, a history, example records). It goes in through the app's real code paths, and it's listed in `docs/DEMO.md` → *Seed data*. If a judge asks, say so.
- **A fallback for a dependency you don't control**: a cached response if the venue wifi or a third-party API dies mid-demo. The live path is the default; the fallback is the safety net, and you say so if it kicks in.
- **Stubs while building**: a lane's hardcoded stub of its contract unblocks the other lanes. It is scaffolding. A beat that still runs on a stub is not done.

What is not allowed: hardcoded output presented as computed, a recording presented as live, a button that only works for the demo's exact input, a screen that is a picture of a screen.

## Mode 3: rehearsal

Given `docs/DEMO.md` (and ideally a run of the app), report:

1. **Time to wow**: seconds until the moment. Over 30 → reorder the beats.
2. **Dead air**: every wait, load, login, or typing stretch. Each gets pre-filled, pre-warmed, or cut.
3. **Does the script show the best of the app?** Name any strong feature that exists but isn't in the demo, and any beat that shows something ordinary. Swap them.
4. **Off script**: the five things a judge is most likely to try or ask for ("use my photo", "what if it's empty", "go back", "do it again", "turn the wifi off"). Try each in the running app. Anything that crashes or dead-ends is a P0 ticket.
5. **Single points of failure**: each live dependency, with its fallback (cached response, second device). No fallback = a P0 ticket.
6. **The first and last sentence** the presenter says. Write them.
7. **Judge questions**: the three most likely, with honest one-line answers, including "what's seeded?" and "what would you build next?".

Finish with at most three changes, ordered by how much they move the score.

## Mode 4: before vs after (a change of direction)

Someone is thinking of changing the idea, a beat, or the design mid-build. Compare the app and demo as currently decided with how they'd be after the change. Your job is an honest opinion for the person deciding; they may overrule it.

1. Score **both** with the Mode 1 table. Score the *after* as it would really be with the hours left, working for real, not as imagined with unlimited time.
2. Add what Mode 1 doesn't see:
   - **Sunk and salvage**: which finished or in-flight tickets are thrown away, and which carry over.
   - **Lanes disturbed**: who has to stop and re-plan. A change inside one lane is cheap; one that moves a contract is not.
   - **Time**: hours left (from `docs/BRIEF.md`) against hours the change needs to be working for real again. Past the halfway point, a change must be clearly better, not slightly better.
3. Verdict, exactly one:
   - **SWITCH**: the after scores higher and can be working for real with time to rehearse.
   - **SWITCH, BUT CUT <x>**: the new direction is better, and it only fits if <x> is dropped or narrowed.
   - **KEEP**: the gain doesn't pay for the disruption. If part of the idea is good, name the smallest piece that could be added to the current app instead.

Output the two one-liners with their scores, gains, costs, and the verdict with a one-line reason. No table unless asked.
