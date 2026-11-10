---
name: hack-run
description: Build and launch the app in one step, in Claude's built-in iOS simulator panel when this session has it, and look at the result. Use when the user says run it, launch, test it, try it, show me, does it work, or after any change that affects what a judge sees.
---

One step for the user: they say "run it" (or type `/hack-run`) and the app appears beside the chat, ready to tap.

## iOS, in the Claude desktop app (this session has the iOS Simulator tool)

1. `scripts/hack sim` boots the simulator and prints its id.
2. Call the iOS Simulator tool with `attach` and that id. The live panel opens next to the chat **before** the build, so the user watches it come up.
3. `scripts/hack run --panel` builds, installs and launches into that same simulator.
4. Look for yourself: take a `screenshot` with the tool, and `tap` through the flow you changed.

The user tests by tapping in the panel. Tell them in one line what to try ("Tap the gear: Settings should slide up").

## Everywhere else

```bash
scripts/hack run
```

It picks the right thing for this repo: the command in `.hack/lanes.json` → `run.command` if set; otherwise an Xcode project (builds, boots an iPhone simulator, opens the Simulator window, installs, launches, saves a screenshot to `.hack/state/last-run.png`); otherwise `npm run dev` (start that one in the background, then open the dev URL in the browser pane).

## Verify, don't assume

Check the ticket's *Done when* list against what you actually see on screen. Report what you saw, not what the code should do. A screenshot taken straight after a tap can catch the screen mid-animation; take it again before deciding something failed.

## When it fails

- Build errors are printed; the full log is at `.hack/state/build.log`.
- The failure is in **another lane's** code → don't fix it there. Confirm with `git log -3 -- <file>`, then `scripts/hack request <lane> "build broken: <error>"` and tell the user. `prod` is always the last known-good build.
- The kit itself misbehaved (wrong simulator, panel didn't attach, a hook got in the way) → `scripts/hack feedback "<what>" "<details>"`, work around it, carry on.
- No Mac → the command says so and exits cleanly. Ship the change and ask a Mac teammate: `scripts/hack request <lane> "please run + screenshot <beat>"`.
