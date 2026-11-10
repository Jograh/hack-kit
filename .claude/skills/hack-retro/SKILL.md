---
name: hack-retro
description: "After the hackathon: turn everything that went wrong or could be better about the kit into clean, de-duplicated issues on the kit's own repo."
disable-model-invocation: true
---

Adapted from Matt Pocock's `retro`. His looks at one coding session and suggests improvements to the agent's environment. This one looks at a whole hackathon and improves **the kit** (the skills, hooks, `scripts/hack`, the rules in `AGENTS.md`) for the next one. It is not about the app that was built.

Nothing here gets fixed during the event. During the event agents only log (`scripts/hack feedback`); this skill is the clean-up afterwards.

The kit repo is `.kit` in `.hack/lanes.json`. Issues there carry the label `kit-feedback`.

## 1. Gather

- **Logged live**: `gh issue list -R <kit> --label kit-feedback --state open --limit 200 --json number,title,body,comments`. Keep the ones whose body names this repo. People without write access to the kit repo can't apply labels, so also take open issues whose title starts with `[kit-feedback]` (`gh issue list -R <kit> --search '"[kit-feedback]" in:title'`); if you can, add the label and drop the prefix.
- **Notes that couldn't be filed**: every `docs/lanes/*/KIT-NOTES.md`.
- **This repo's tracker**: issues labelled `request` or `needs-human` that were really kit problems (a guard that blocked legitimate work, a ticket template that confused a worker), and any PR that touched `.claude/`, `scripts/`, `.githooks/` or `AGENTS.md`: that is a kit fix made mid-event which the template doesn't have yet.
- **The people**: ask the user once, "What annoyed you or slowed you down that the agents wouldn't have noticed?" and take their answer as given.
- **This session**, if the user asks for it: where did the agent loop, get blocked by a hook, or ask something it should have known?

## 2. Sort

Merge duplicates, then put each finding in one category:

- **Guardrails**: a hook or check that blocked legitimate work, or missed something it should have caught.
- **Orchestration**: dispatch, collect, the board, workers that needed a person too often.
- **Run and test**: build, simulator, verification.
- **Tickets and lanes**: tickets that crossed lanes, were too big, or weren't checkable alone; lane splits that caused requests.
- **Judge focus**: time spent on things no judge saw; staging that should have happened sooner.
- **Rules that did nothing**: instructions in `AGENTS.md` or a skill that no agent followed or needed. Removing them is an improvement.
- **Fixed mid-event**: already solved in this repo, needs porting to the template (link the PR).

For each: what happened, how often, what it cost (minutes, a broken demo, a confused teammate), and the smallest change that would prevent it. Prefer a change to a script or hook over a new rule in prose: a check always runs, a rule gets skimmed.

## 3. Show the user (one pass)

A short list, most costly first: title · category · times hit · proposed fix. Ask once: anything wrong, missing, or not worth filing? Apply the answers.

## 4. File

On the kit repo, label `kit-feedback`:

- A finding that already has an issue: edit it into the clean form below, and close its duplicates with a comment pointing to it.
- A new finding: create it.
- Delete nothing. Leave `KIT-NOTES.md` files in place and add a line at the top: "Filed by /hack-retro on <date>".

<issue-template>

## What happened

<one or two sentences, with the command or hook involved>

## How often, and what it cost

<times hit, by which lanes; what it cost>

## Proposed fix

<the smallest change: which file in the kit, what it should do instead>

## Seen in

<repo, and the issue/PR numbers there>

</issue-template>

Finish by telling the user how many issues were filed and the three that would matter most next time.

If the kit repo can't be reached (no access), write the same list to `docs/KIT-RETRO.md` instead and say so.
