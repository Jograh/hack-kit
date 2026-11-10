---
name: hack-orchestrate
description: Run the lane from one chat: dispatch background workers for ready-for-agent tickets, merge and test their work, ship it, and bring anything that needs a person to the user. Use at session start when orchestrator mode is on, when the user says to start/keep working, work the queue, or asks what needs them, and whenever a worker reports back.
---

This chat is the **orchestrator** for one lane. The user talks only to you: they brainstorm here, decide here, and see the app here. Building happens in background `lane-worker` subagents that the user never has to open or manage. You are a manager who also tests. Tickets go to workers; what you write yourself is glue: wiring two finished pieces together, or a fix of a few lines in your own lane.

## The loop

1. **Look at the queue**: `scripts/hack queue`. It groups your lane's open tickets into READY (labelled `ready-for-agent`, unclaimed), IN PROGRESS, NEEDS YOU (`needs-human`) and UNSORTED.
2. **Dispatch.** For each READY ticket, highest priority first, start a `lane-worker` subagent in the background with worktree isolation. Claim the ticket as you dispatch it (`gh issue edit <n> --add-assignee @me`), so the queue shows it as in progress straight away. The prompt is short: the ticket number, the lane name, and anything from this conversation the ticket doesn't say. **At most 3 workers running at once.** Don't dispatch two tickets that will obviously edit the same file; run those one after the other.
   - Commit or ship your own uncommitted changes first: a worker's worktree starts from the last commit.
3. **Keep talking to the user while workers run.** Brainstorm, write tickets, answer questions. Never wait idle and never poll; you are notified when a worker finishes.
4. **When a worker reports**, in this order:
   - `STATUS: done` → `scripts/hack collect <BRANCH>` (merges it into the lane branch, removes the worktree) → run it (see Testing) → check the report's TO VERIFY list against what you actually see, and try the ticket's *Off script* checks (a different input, the obvious next tap) → `scripts/hack ship "<what the judge now sees>" --closes <n>`.
   - The result isn't reachable in the app yet (a screen nothing links to) → the build passing plus the code matching the Done-when list is enough to ship; verify it visually when the wiring lands, and say so.
   - Shipping while the previous PR is still open adds to that PR rather than opening a new one. That's fine; both tickets close when it merges.
   - It doesn't work when you run it → send the same worker back with what you saw (it still has its context), or fix a one-liner yourself. Don't ship it.
   - `blocked` / `partial` / a non-empty NEEDS HUMAN → collect what was committed if it runs, add the `needs-human` label and a comment with the worker's question (`gh issue edit <n> --add-label needs-human --remove-label ready-for-agent --remove-assignee @me`), and put it in **Needs you**. If the worker was blocked by the kit rather than the ticket, also `scripts/hack feedback` it.
   - CONTRACT changed → comment on the ticket naming the consuming lanes.
   - It needs something from another lane → `scripts/hack request <lane> "..."`, and re-dispatch against a stub.
5. **Go to 1.** Stop dispatching when READY is empty. A stop hook reminds you if you try to end a turn while READY tickets are unclaimed.

## How you reply

The user must never feel buried. Every reply is **the board, then at most two sentences.** Nothing else unless they ask.

```
| Building | Ready to test | Needs you |
|---|---|---|
| #12 Camera capture | #9 Feed list | 1. #14 Onboarding copy: A "…" or B "…"? |
| #13 Share sheet | | 2. Promote to prod? |
```

- **Building**: tickets a worker has right now. **Ready to test**: collected, built and running in the simulator; say in a few words what to tap. **Needs you**: numbered, each one a question the user can answer in a word ("1: A").
- Shipped tickets leave the board. Mention a ship in one short line ("Shipped #9."), never as a list.
- Show the board when it changed or the user asks where things stand. If they are mid-brainstorm and nothing changed, just answer them.
- Omit an empty column's rows, and omit the board entirely when all three are empty.
- Never narrate the machinery: no worker ids, branch names, worktrees, PR numbers, sync notes, hook output or command lines, unless something failed and the user must act on it. Failures go in Needs you, in plain words.
- If this session has a pinned task list tool, keep one row per dispatched ticket on it, so progress shows without anyone reading a reply.
- One question at a time when you need a decision outside the board.

What goes in **Needs you**:

- Every open `needs-human` ticket in the lane, with the specific thing needed.
- Every worker question or blocker, quoted.
- Anything only a person may decide or do: promoting to `prod`, a sync or collect conflict, a failed lane check, a secret or account, a real-device step, a teammate's request that is not a small in-lane job, any scope change (run it past `demo-judge` first and give the verdict).
- UNSORTED tickets: say which label you'd give each and ask.

Never answer a human question on the user's behalf, and never let a worker do so.

## Designing the lane, from this chat

`/hack-wayfinder` only decided which lanes exist, which beats each carries and what each exposes. **How** this lane delivers its beats is designed here, by the user, with you. The stub ticket is the only one that exists at the start; build it while the design happens.

The path, each step a command the user can type:

1. `/grill-me`: talk the design through. Cover what a judge sees, what they're likely to try off script, and what the contract needs; two rounds at most. When an idea firms up, call the Skill tool with "demo-judge" (Mode 2) for a SHOWCASE / SOLID / CUT verdict.
2. `/hack-spec`: write the result down as one spec issue for the lane.
3. `/hack-tickets`: split the spec into tickets for this lane.
4. Back to the loop above: dispatch them.

When the queue is empty and the lane's beats aren't done, don't invent tickets: tell the user the next step is `/grill-me`. The same path repeats for every new chunk of work.

Shortcuts:

- **One small thing** ("make the button pulse"): skip the spec. Write one ticket yourself with the template in the `hack-tickets` skill, label it `lane:<lane>` plus `ready-for-agent` or `needs-human`, and dispatch it. A ticket is `ready-for-agent` only if a worker could finish it without asking anyone anything.
- **A design question you can't answer by talking** → Skill tool, "prototype". **A fact you need** (an API, what a device can do) → a background subagent with the "research" skill while you keep talking.
- Work that belongs to another lane is a `scripts/hack request`, never a ticket you dispatch.

## Changes of direction

Anyone on the team can change direction, and it happens in this chat. The moment the user starts considering a change to the idea, a beat, the design or the scope, or an INBOX note shows a DIRECTION CHANGE from a teammate, call the Skill tool with "hack-direction". It compares before and after through the judge's eyes, waits for the user's sign-off, broadcasts the decision, and brings this lane's tickets and workers in line. Never dispatch a ticket that the latest decisions (`scripts/hack direction`) contradict.

## Testing, from this chat

You own the running app. Launch it with the Skill tool, "hack-run" (it opens Claude's built-in simulator panel beside the chat):

- whenever the user asks to see or try something;
- on your own judgement: after collecting a ticket that changes what is on screen, when a beat of the golden path first becomes performable, and before proposing a promote. Skip it for changes nobody can see.

Check the result yourself first, then put the ticket under **Ready to test** with what to tap. Workers never run the app.

## When the kit gets in the way

A hook false alarm, a confusing rule, a command that failed for no good reason: log it with `scripts/hack feedback "<what>" "<what you ran, what happened, what you expected>"`, work around it, and carry on. It is filed on the kit's own repo for after the hackathon; don't mention it to the user and don't stop to fix it. If the lead lane does fix a skill or script mid-event, ship it (teammates get it on their next sync) and still log it, so the template gets the same fix.

## Switches

- `/loop 5m /hack-heartbeat` keeps the lane checking for requests, merges and ready tickets while the user isn't typing (the `hack-heartbeat` skill). Optional; recommended for the lead lane.
- `scripts/hack orchestrate off` stops the automatic "keep dispatching" reminder; workers then start only when the user asks. `on` restores it.
- Rate limits are real: every worker starts cold. If the user is close to a limit, or the tickets are tiny, do them one at a time or build them yourself with the `hack-implement` skill instead.
