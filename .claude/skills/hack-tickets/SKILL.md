---
name: hack-tickets
description: "Break your lane's spec (or the current conversation) into tickets a background worker can build, and publish them as GitHub issues under the map. At kick-off the lead uses it to publish one stub ticket per lane."
disable-model-invocation: true
---

Adapted from Matt Pocock's `to-tickets`. Still tracer bullets: each ticket is a thin, complete, demoable slice. The hackathon change: **a ticket never crosses a lane.** Work that spans lanes is split at the contract, one ticket per side, and neither side blocks the other because both build against the stub.

Tracker operations are in `docs/agents/issue-tracker.md`. Lanes are in `.hack/lanes.json`. The destination is `docs/DEMO.md`.

## 1. Gather

Two modes:

- **Your lane (the normal case).** Each person runs this in their own chat, after designing their lane (`/grill-me` → `/hack-spec`). Work from the spec issue (passed as a number, or the open issue labelled `spec` + `lane:<your lane>`), the conversation, `docs/DEMO.md` and your `docs/lanes/<lane>/CONTRACT.md`. You publish tickets **for your own lane only**. Something another lane must do is a `scripts/hack request`, not a ticket.
- **Kick-off (called by `/hack-wayfinder`).** Publish exactly one ticket per lane: the stub ticket below. Stop there; each owner writes the rest.

## 2. Draft

In this order:

1. **Stub ticket** (always first, `P0-golden-path`): a hardcoded implementation of the lane's CONTRACT, shipped in ≤30 min, so consumers are unblocked immediately. Skip it if kick-off already published it.
2. **Golden-path tickets** (`P0-golden-path`): one per demo beat the lane carries. Done = the beat can be performed, staged or not.
3. **Make-it-real tickets** (`P1-real`): replace a stage with the real thing, only where `demo-judge` says REAL.
4. **Polish tickets** (`P2-polish`): things a judge notices. Nothing else.

Rules:

- Exactly one `lane:<name>` label. If you can't say which single lane's folders it touches, split it.
- Exactly one of `ready-for-agent` or `needs-human`. `ready-for-agent` means a background worker can finish it without asking anyone anything: the outcome, the staged-or-real call and the contract are all written down. `needs-human` is for a decision, an account or key, a physical device, or a judgement of taste; say in the body exactly what the person must do. Aim for most tickets to be `ready-for-agent`: when one isn't, try splitting the human part into its own small ticket that blocks nothing.
- Sized for one agent session, ≤60 minutes of wall clock. Bigger → split.
- *Done when* must be checkable when this ticket lands alone. If a screen can't be reached until another ticket wires it in, either include the wiring or make the check "builds, and the view matches this description".
- **Blocked by: none** is the goal. A ticket may be blocked only by a ticket *in its own lane*. Across lanes, depend on the contract, never on a ticket.
- A change to a contract is its own ticket in the **owning** lane, and its body names the consuming lanes.
- When unsure whether something deserves a ticket, call the Skill tool with "demo-judge" (Mode 2). CUT items go to the Cut list, not the tracker.

## 3. Check with the user (one pass)

Show the breakdown: title · priority · beat · ready-for-agent or needs-human · blocked by. Ask once: anything too big, missing, or should be cut? Apply the answers and publish. Do not iterate further; tickets are cheap to fix later.

## 4. Publish

One issue per ticket, each lane's in dependency order, title prefixed `[<lane>]`, labels `lane:<lane>` + its priority label + `ready-for-agent` or `needs-human`, as a sub-issue of the map where the tracker supports it (else `Part of #<map>` on the first line). Leave them unassigned: the owner's agent claims them.

<issue-template>

Part of #<map>

## Beat

<which row of the golden path this serves, quoted. "none" is only valid for P2-polish>

## What the judge sees when this is done

<one or two sentences, from the audience's seat>

## Real or staged

<REAL, or STAGE: what the staged version is>

## Done when

- [ ] <a check someone can perform by running the app: `scripts/hack run`, then …>
- [ ] Shipped to dev with `scripts/hack ship`

## Contract touched

<"none", or the exact addition/change to docs/lanes/<lane>/CONTRACT.md and which lanes consume it>

## Blocked by

<"None", or a ticket in this same lane>

</issue-template>

No file paths beyond the lane's folder, no code, unless a snippet *is* the contract (a type, a JSON shape, a function signature).

After publishing: comment on the spec issue with the ticket numbers and close it (the tickets carry the work now), and comment on the map: "Tickets published for <lane>: #a, #b, #c". That comment is a broadcast; every agent sees it. Then tell the user the next command: `/hack-orchestrate`.
