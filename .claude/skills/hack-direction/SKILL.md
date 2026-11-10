---
name: hack-direction
description: Handle a change of direction, from the first "what if we…" to every teammate's agent knowing about it. Use when the user is thinking about changing the idea, the demo, a beat, the design, the scope, or anything another lane builds against; and when an INBOX note shows a DIRECTION CHANGE from a teammate.
---

Ideas refine themselves while building. Anyone on the team may change direction. Two things must be true every time: the person deciding has seen what it does to the demo, and every other agent finds out straight away.

The record is `docs/decisions/` (one new file per decision, so never a merge conflict) plus a comment on the pinned **Direction** issue, which hooks push into every agent's context. `scripts/hack decide` writes both. `scripts/hack direction` prints the latest.

## A. The user is thinking of a change

Start as soon as they are *considering* it, not once they've decided.

1. **Pin down before and after**, one line each, in what-the-judge-sees terms. Before comes from `docs/DEMO.md` and `scripts/hack direction`. If the after is fuzzy, ask one question to sharpen it.
2. **Judge it**: call the Skill tool with "demo-judge" (Mode 4: before vs after).
3. **Show the user**, short enough to read in ten seconds:

   ```
   **Before** <one line>            <score>/60
   **After**  <one line>            <score>/60
   Gains: <what the judge sees that they didn't>
   Costs: <tickets thrown away, lanes that must change, hours>
   Verdict: SWITCH | KEEP | SWITCH, BUT CUT <x>   — <one-line reason>
   ```

   This is an opinion. The user decides, and may overrule it.
4. **Wait for sign-off.** Show the exact text you will broadcast and ask "Send this to the team?". Nothing is sent, and no ticket or code changes, before a clear yes. A "maybe" or "let me think" is not a yes.
5. **On yes**:

   ```bash
   scripts/hack decide "<what changed, one line>" "**Before:** …
   **Now:** …
   **Why:** …
   **Judge:** <verdict and scores>
   **Each lane must:** <lane: what changes for them>" --affects <lane,lane>
   ```

   Omit `--affects` when it touches everyone. The app asks the user to confirm the command; that is the sign-off on the wording.
6. **Then fix your own lane** (section C), and ship so the decision file reaches `dev`.

If they decide against it, drop it. Nothing is recorded.

## B. A teammate changed direction (INBOX shows "DIRECTION CHANGE")

1. Read it in full: `scripts/hack direction 1`.
2. It names your lane or all lanes → do section C now. Otherwise note it and carry on.
3. Tell the user in one line what changed and what it does to their lane. If they object, they talk to the teammate or make their own change with section A; you don't argue on the issue for them.

## C. Bring a lane in line with a decision

- **Running workers** building something the decision removes or changes: stop them or let them finish and discard; don't collect stale work.
- **Open tickets** that are now wrong: comment "Superseded by decision <file>" and close, or edit the ticket to match. Write replacements with the `hack-tickets` template.
- **Your CONTRACT.md** if what you expose changed. Name the consuming lanes on the ticket.
- **Lead lane only**: if the golden path changed, update `docs/DEMO.md` to match and ship it. `DEMO.md` always describes the demo as currently decided.
- Put anything you can't settle alone under **Needs you**.

A decision overrides older tickets, CONTRACTs and docs. When two decisions disagree, the newer one wins.
