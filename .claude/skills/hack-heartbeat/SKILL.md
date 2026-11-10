---
name: hack-heartbeat
description: "One heartbeat tick for the lane: check what changed while the chat was idle (teammates' merges, requests, ready tickets, and for the lead whether the demo still works) and act on the small things. Meant to be run on a timer with /loop; optional, recommended for the lead. Use when the user asks for a heartbeat, to keep the lane moving while they are away, or to check in on a schedule."
---

The kit's hooks only fire when something happens in this chat. While the user is away from the keyboard, requests from teammates sit unseen and `dev` drifts. A heartbeat closes that gap. It matters most for the **lead lane**, because every other lane's requests for dependencies, config and wiring end up there.

Start it (the user types this, or you start it with the Skill tool, "loop", when they say yes):

```
/loop 5m /hack-heartbeat
```

5 minutes for the lead; 10 to 15 for other lanes. Stop it by telling the chat to stop the loop.

## One tick

1. Run `scripts/hack pulse`. It pulls `dev`, reads the inbox, and lists unclaimed ready tickets. It prints only what is new.
2. **`PULSE: quiet` → end the turn immediately** with the single word "Quiet." Do not look at the queue, re-read files, summarise, or show the board. An empty tick must cost almost nothing.
3. Otherwise act on what it printed, smallest first:
   - **INBOX, a request that is small and inside this lane** (add a field, expose a value, add a dependency, wire a shipped screen into the shell): do it, run it if it changes what's on screen, ship it, reply on the issue.
   - **INBOX, anything larger, or any question for the person**: leave it. Add it to **Needs you**.
   - **INBOX, DIRECTION CHANGE**: bring the lane in line (Skill tool, "hack-direction", sections B and C).
   - **SYNC**: no action, unless it names a CONTRACT this lane consumes (re-read it), or, in the lead lane, a teammate shipped something the app shell doesn't reach yet (wire it in and ship).
   - **READY tickets**: dispatch workers as in the `hack-orchestrate` loop (at most 3 running).
   - **DEMO CHECK DUE** (lead lane only, at most every 30 minutes and only when `dev` has changed): launch the app (Skill tool, "hack-run"), perform every beat of `docs/DEMO.md`, then `scripts/hack pulse --demo-checked`. A beat that fails → `scripts/hack request <owning lane> "golden path beat <n> broken: <what you saw>"`. All beats pass and `prod` is behind → put "Promote to prod?" under **Needs you**.
4. Reply as the orchestrator does: the board if it changed, plus one line on what this tick did. Nothing else.

## Never on a heartbeat

The person may not be watching. A tick must not:

- promote to `prod`, or send a direction change (`scripts/hack decide`);
- answer a `needs-human` question, or pick between options on the user's behalf;
- start a large request, redesign anything, or write tickets nobody asked for;
- spend more than a few minutes. If something is bigger than that, it goes under **Needs you** and the tick ends.

If the user is in the middle of a conversation with you when a tick fires, handle it in one line and return to what they were doing.

## Cost

Every tick that finds something is a full turn in a long chat. If the user is near a rate limit, lengthen the interval or stop the loop; the lane still works without it, just only when they prompt.
