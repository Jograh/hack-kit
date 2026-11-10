---
name: hack-promote
description: "Promote dev to prod: run the golden path, and if every beat performs, fast-forward the demo branch and tag it."
disable-model-invocation: true
---

`prod` is the branch the demo runs from. Its only rule: **it is never broken.** It moves forward only from `dev`, only after the golden path has just been performed.

1. Make sure you're current: `scripts/hack sync`.
2. `scripts/hack run`, then perform every beat in `docs/DEMO.md` → *Golden path*, in order, looking at the app each time. Report beat by beat: ✓ or ✗ with what you saw.
3. Any ✗ → **do not promote.** File it: `scripts/hack request <owning lane> "golden path beat <n> broken: <what>"` and stop.
4. All ✓ → show the user the list of commits about to be promoted and ask for the go-ahead. On yes: `scripts/hack promote --yes`.
5. Report the new `demo-*` tag. That tag is the rollback point: on the demo machine, `git checkout <tag>` then `scripts/hack run`.

Promote after every beat that newly works, not once at the end. In the last hour, promote only fixes; in the last 20 minutes, don't promote at all: the demo runs from the last tag.
