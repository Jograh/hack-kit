---
name: hack-promote
description: "Promote dev to prod: run the golden path, and if every beat performs, fast-forward the demo branch and tag it."
disable-model-invocation: true
---

`prod` is the branch the demo runs from. Its only rule: **it is never broken.** It moves forward only from `dev`, only after the golden path has just been performed and the app has survived being poked at.

1. Make sure you're current: `scripts/hack sync`.
2. `scripts/hack run`, then perform every beat in `docs/DEMO.md` → *Golden path*, in order, looking at the app each time. Report beat by beat: ✓ or ✗ with what you saw.
3. **Go off script.** A judge will. For each beat try at least one thing the script doesn't: a different input, the obvious next tap, going back, doing it twice, an empty state. Then relaunch the app cold and check it comes up. Use `docs/DEMO.md` → *Off script* as the list. A crash, a dead end, or a beat that only works because it still runs on a stub is a ✗.
4. Any ✗ → **do not promote.** File it: `scripts/hack request <owning lane> "golden path beat <n> broken: <what>"` and stop.
5. All ✓ → show the user the list of commits about to be promoted and ask for the go-ahead. On yes: `scripts/hack promote --yes`.
6. Report the new `demo-*` tag. That tag is the rollback point: on the demo machine, `git checkout <tag>` then `scripts/hack run`.

Promote after every beat that newly works, not once at the end. In the last hour, promote only fixes; in the last 20 minutes, don't promote at all: the demo runs from the last tag.

## The demo phone (iOS)

Right after a successful promote, if the demo runs on a real iPhone, offer to put the promoted build on it: `scripts/hack run --device --prod` (the `hack-device` skill). It builds from a clean checkout of `prod`, so unshipped work in this folder can't leak into the demo. Ask the user to open the app on the phone and confirm the first beat.
