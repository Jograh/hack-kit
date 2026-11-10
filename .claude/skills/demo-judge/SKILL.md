---
name: demo-judge
description: Score an idea, a ticket, or a demo script the way a hackathon judge will experience it (wow factor and demonstrability over technical depth). Use when choosing between ideas, deciding whether something is worth building or should be staged or cut, when someone proposes extra scope or a change of direction (before vs after), or before a demo rehearsal.
---

A judge gives you about three minutes, has seen thirty other projects, and scores what they **saw**, not what you built. This skill makes every decision from that seat. Technical impressiveness counts only where it is visible.

Read `docs/BRIEF.md` (judging criteria, hours left) and `docs/DEMO.md` if they exist. If the brief has the real criteria and weights, score against those too and let them override the default weights below.

## Mode 1: ideas

Score each idea 1–5 per row; total = Σ score × weight (max 60).

| Criterion | ×  | 5 looks like | 1 looks like |
|-----------|----|--------------|--------------|
| **Wow moment** | 3 | One visible instant that gets a reaction, in the first 30 seconds | Value appears only after explanation |
| **Demonstrability** | 3 | Runs live, on stage, visibly, and a judge could drive it | Needs slides, a backstory, or "imagine that…" |
| **Criteria fit** | 2 | Hits the top-weighted judging criteria and a sponsor prize | Good project, wrong hackathon |
| **Demo-path feasibility** | 2 | The whole team in parallel reaches a working golden path with half the time left | Critical path runs through one person or one unknown |
| **Story** | 1 | Problem → magic → payoff fits one sentence | Needs a paragraph |
| **Stage safety** | 1 | Survives bad wifi, a cold start, a nervous presenter | Depends on a live third party behaving |

Output one table (ideas × criteria, totals), then for the top two: *the moment* in one line, the biggest risk, and what would be staged. Recommend one. Be blunt about a favourite that scores badly.

## Mode 2: a ticket or a scope proposal

Ask one question: **does a judge see this in the 90 seconds?** Then give exactly one verdict:

- **REAL**: it is on the golden path and a judge can tell real from staged (it's interactive, it takes their input, it *is* the moment). Build it properly.
- **STAGE**: a judge sees the *result* but not the mechanism. Hardcode it, pre-compute it, replay a recording, seed the data. Say what the staged version is in one line. This is the default for anything slow, flaky, or behind a network call.
- **CUT**: no judge sees it (auth, settings, error states, scaling, a second platform, tests for staged code). Add it to the Cut list in `docs/DEMO.md`.

Rule of thumb for effort: the moment gets the most real engineering; everything upstream of it gets the least that makes the moment believable; everything downstream of it gets polish (animation, copy, a loading state that looks intentional).

Staging is craft, not deceit: the demo shows the product as it would work. When a judge asks "is that live?", the answer is the truthful one-liner in `docs/DEMO.md` → *Staged honestly*. Never claim a staged thing is real; many hackathons disqualify for it, and "that part is seeded for the demo, here's the part that's live" lands fine.

## Mode 3: rehearsal

Given `docs/DEMO.md` (and ideally a run of the app), report:

1. **Time to wow**: seconds until the moment. Over 30 → reorder the beats.
2. **Dead air**: every wait, load, login, or typing stretch. Each gets pre-filled, pre-warmed, or cut.
3. **Single points of failure**: each live dependency, with its fallback (cached response, recorded video, second device). No fallback = a P0 ticket.
4. **The first and last sentence** the presenter says. Write them.
5. **Judge questions**: the three most likely, with one-line answers.

Finish with at most three changes, ordered by how much they move the score.

## Mode 4: before vs after (a change of direction)

Someone is thinking of changing the idea, a beat, or the design mid-build. Compare the demo as currently decided with the demo after the change. Your job is an honest opinion for the person deciding; they may overrule it.

1. Score **both** with the Mode 1 table. Score the *after* as it would really be on stage with the hours left, not as imagined with unlimited time.
2. Add what Mode 1 doesn't see:
   - **Sunk and salvage**: which finished or in-flight tickets are thrown away, and which carry over.
   - **Lanes disturbed**: who has to stop and re-plan. A change inside one lane is cheap; one that moves a contract is not.
   - **Time**: hours left (from `docs/BRIEF.md`) against hours the change needs to reach a performable golden path again. Past the halfway point, a change must be clearly better, not slightly better.
3. Verdict, exactly one:
   - **SWITCH**: the after scores higher and the golden path is performable again with time to rehearse.
   - **SWITCH, BUT STAGE <x>**: the new moment is better, and it only fits if <x> is hardcoded, seeded or replayed.
   - **KEEP**: the gain doesn't pay for the disruption. If part of the idea is good, name the smallest piece that could be added to the current demo instead.

Output the two one-liners with their scores, gains, costs, and the verdict with a one-line reason. No table unless asked.
