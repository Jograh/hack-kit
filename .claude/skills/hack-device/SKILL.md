---
name: hack-device
description: Put the iOS app on a real iPhone over a cable: walk the one-time setup, then build, install and launch in one step. Use when the user says run it on my phone, put it on the iPhone, install on the device, test on a real phone, set up my phone, or get the demo phone ready.
---

iOS stack only. The full guide is `stacks/ios/DEVICE.md`; read it once. A free Apple ID is enough.

## 1. Check

```bash
scripts/hack device
```

It prints a checklist and ends with "Ready" or with the steps still missing.

## 2. Not ready → walk the user through it

The missing steps are things only a person can do: signing in to Xcode, tapping Trust, switching on Developer Mode. For each ✗ line:

- Give the user that **one** step, with the exact menu path from the checklist. One step at a time, not the whole list.
- Wait for them to say it's done, then run `scripts/hack device` again.
- Never ask for, type, or handle their Apple ID password. They sign in to Xcode themselves.

If there is more than one Apple team or more than one phone, show the choices and set the user's pick: `scripts/hack device --team <ID>` / `--phone "<name>"`.

## 3. Ready → run

```bash
scripts/hack run --device            # the user's current work
scripts/hack run --device --prod     # exactly what was last promoted (the demo phone)
```

Use `--prod` whenever the phone is for the judges or the user says "demo build".

The first install from a free Apple ID opens with "Untrusted Developer". Tell the user: Settings → General → VPN & Device Management → their Apple ID → Trust. Then run the command again.

## 4. Verify

You cannot see or tap a real phone. Ask the user one specific question about what is on screen ("Does Home show three cards?"), and take their answer as the result. Do your own checking in the simulator with the `hack-run` skill; the phone is for the user and the judges.

## When it fails

The command prints a "→" line for the usual causes; relay it as the next step. Otherwise read `.hack/state/device-build.log` and the table at the end of `stacks/ios/DEVICE.md`.

- Never fix signing by editing `project.pbxproj`, and never commit a signing change. Signing is passed on the command line from `.hack/local.json`.
- A build error in Swift code is an ordinary bug: it fails in the simulator too, so fix it there.
- Stuck for more than 15 minutes with the demo close → the simulator on the Mac is the fallback. Say so plainly, and log it: `scripts/hack feedback "<what failed>" "<log excerpt>"`.
