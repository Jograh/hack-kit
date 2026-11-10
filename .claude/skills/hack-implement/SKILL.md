---
name: hack-implement
description: "Take the next ticket in your lane from claim to merged-in-dev: claim it, build it inside your folders, run it, ship it. Use when the user says to work the next ticket, pick up an issue, or build something in their lane."
---

Adapted from Matt Pocock's `implement`. The hackathon change: the unit of done is **merged into dev and visible in the running app**, and you never leave your lane to get there.

## Loop

1. **Pick.** If the user named a ticket, use it. Otherwise run `scripts/hack inbox --all` and take the first open, unclaimed ticket for your lane: `P0-golden-path` before `P1-real` before `P2-polish`; `request` tickets from teammates jump the queue when they are small. State the ticket's title before starting.
2. **Claim.** `gh issue edit <n> --add-assignee @me`. First write, before any code.
3. **Read.** The ticket and its comments, `docs/DEMO.md` (the beat you serve), your own `docs/lanes/<lane>/CONTRACT.md`, and the CONTRACT of any lane you consume.
4. **Build, inside your lane.** Stub first, then fill in. If the ticket says STAGE, stage it: hardcode, seed, or replay, and note it under *Staged honestly* in your lane docs (`docs/lanes/<lane>/STAGED.md`). No tests for staged code. No abstractions "for later". There is no later.
5. **Run it.** `scripts/hack run`, then look at the result (the screenshot it writes, the simulator, or the browser) and check the ticket's *Done when* list. Fix until the beat performs. If it breaks, call the Skill tool with "diagnosing-bugs" — but timebox it: 15 minutes, then stage around the bug and write down what you did.
6. **Ship.** `scripts/hack ship "<what the judge now sees>" --closes <n>`. That commits, pulls dev, opens the PR and merges it. If you changed your CONTRACT, say so in a comment on the ticket and name the consuming lanes: `gh issue comment <n> --body "..."`.
7. **Next.** Go to 1. Ship small and often: a merged stub beats an unmerged masterpiece, and every merge is what keeps the other three lanes unblocked.

## When you hit the lane wall

The lane guard will refuse writes outside your folders. Do not route around it with Bash. Instead, in this order:

1. Can you do it inside your lane? (Wrap it, adapt it, stub it.) Do that.
2. Need something from another lane? `scripts/hack request <lane> "<what>" "<why, and the shape you need>"`. Then **keep going** against a stub of what you asked for. Never wait.
3. Need a root/shared file changed (dependency, config, app shell)? Same thing, addressed to the lead lane.

## When something arrives for you

Hooks put teammates' merges and new issue activity into your context as `SYNC:` and `INBOX` notes. A `SYNC` note needs no action unless it mentions a CONTRACT you consume. An `INBOX` item is a person waiting: read the thread, and if it's a small in-lane ask, do it now and reply on the issue; otherwise tell the user and let them decide the order.

## Scope creep

If, mid-ticket, you or the user think of something extra, call the Skill tool with "demo-judge" (Mode 2). REAL/STAGE → new ticket (`gh issue create` with your lane label). CUT → one line in a new file under `docs/lanes/<lane>/`. Then back to the ticket in hand.
