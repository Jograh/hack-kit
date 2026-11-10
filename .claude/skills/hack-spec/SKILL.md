---
name: hack-spec
description: "Write down the design you just talked through for your lane as one short spec issue, ready to be split into tickets. No interview: it synthesises the conversation. Use when the user types /hack-spec, says to write the design down or spec it, or when a design discussion in the lane has settled and the next step is tickets."
---

Adapted from Matt Pocock's `to-spec`. Same rule: **do not interview the user**; synthesise what was already discussed (usually a `/grill-me` session in this chat). The hackathon changes: the spec covers **one lane**, it is written from the judge's seat, and it is short. No user stories, no testing plan.

`/hack-wayfinder` decided which beats this lane carries and what it exposes. The spec says **how** this lane will deliver them, for real: everything in it must work when a judge goes off script, not only along the demo.

## Process

1. Read `docs/DEMO.md` (your beats), `docs/lanes/<lane>/CONTRACT.md`, the CONTRACT of each lane yours consumes, and the latest decisions (`scripts/hack direction`). Look at what already exists in your lane's folders.
2. If the conversation leaves a real gap (a beat with no design at all), say which one and stop: the user should `/grill-me` on it first. Don't fill gaps by guessing, and don't start asking questions here.
3. For anything in the design whose place isn't settled (a highlight, something the app just needs, or not worth building), call the Skill tool with "demo-judge" (Mode 2: SHOWCASE / SOLID / CUT).
4. Write the spec with the template below and show it to the user once. Apply their corrections.
5. Publish it: one issue titled `[<lane>] SPEC: <name>`, labels `lane:<lane>` and `spec`, with `Part of #<map>` on the first line. It carries neither `ready-for-agent` nor `needs-human`: a spec is not built, its tickets are.
6. If the design changes what the lane exposes, update `docs/lanes/<lane>/CONTRACT.md` now and name the consuming lanes in the spec. If it changes a beat, the idea or another lane's work, that is a change of direction: call the Skill tool with "hack-direction" instead of publishing.
7. Tell the user the next command: `/hack-tickets`.

<spec-template>

Part of #<map>

## Beats this covers

<the rows of the golden path this lane carries, quoted from docs/DEMO.md>

## What the judge sees

<the screens and moments, in order, from the audience's seat. A few sentences per beat.>

## Off script

<what a judge is likely to try around these beats (their own input, the next tap, an empty state, no network) and what the app does in each case. These become SOLID tickets.>

## Seed data

<"none", or the sample content preloaded so the app doesn't look empty, and how it gets in>

## Design decisions

<the choices made in the conversation, one line each: screens and their states, the data shapes, the service or model used, what happens on failure during a demo>

## Contract

<what this lane exposes and consumes after this design: "unchanged", or the exact additions and which lanes consume them>

## Out of scope

<what was discussed and dropped. These do not become tickets.>

</spec-template>

No file paths outside the lane's folder and no code, unless a snippet *is* the decision (a type, a JSON shape, a function signature).
