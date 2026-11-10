# Running on a real iPhone (cable)

A free Apple ID is enough. Nobody needs a paid developer account.

## One-time setup, per person and per Mac (about 10 minutes)

Do this the night before, not at the venue. `scripts/hack device` checks each step and says which one is missing.

1. **Apple ID in Xcode.** Xcode → Settings → Accounts → **+** → Apple Account. Sign in with any Apple ID.
2. **Cable and trust.** Plug the iPhone into the Mac, unlock it, tap **Trust** and enter the passcode.
3. **Let Xcode prepare the phone.** Open Xcode → Window → Devices and Simulators, and wait until the phone shows no warnings. This can take a few minutes the first time.
4. **Developer Mode.** On the phone: Settings → Privacy & Security → Developer Mode → on. The phone restarts; confirm when it comes back. (The option only appears after step 3.)
5. **Check:** `scripts/hack device` should end with "Ready".
6. **First install:** `scripts/hack run --device`. With a free Apple ID the app installs but iOS won't open it yet. On the phone: Settings → General → VPN & Device Management → tap your Apple ID → **Trust**. Run the command again.

## Every time after that

Plug in, unlock, then:

```bash
scripts/hack run --device          # your current work
scripts/hack run --device --prod   # exactly what was last promoted: use this for the demo phone
```

## How signing stays out of git

Signing normally writes each person's Apple team into `project.pbxproj`, which would conflict the moment two people did it. Here it never touches the project:

- Your team id is stored in `.hack/local.json` (not committed) and passed to the build on the command line.
- Your bundle id gets your GitHub username added (`dev.hackkit.recall.maya-k`), because two free Apple IDs can't install the same bundle id. Override with `.ios.bundle` in `.hack/local.json` if the app needs a fixed one.
- More than one Apple team on your Mac? `scripts/hack device --team <ID>`. More than one phone? `scripts/hack device --phone "<name>"`.

Never open the project in Xcode and pick a team under Signing & Capabilities and then commit. If you had to do it to get unstuck, `git restore <App>.xcodeproj/project.pbxproj` before shipping.

## Limits of a free Apple ID

- **Installs expire after 7 days.** Reinstall on the day of the demo.
- **Only a few new app ids per week.** Don't rename the app or change its bundle id during the event.
- **Some capabilities are unavailable**, including push notifications, Sign in with Apple, iCloud and Apple Pay. If the idea needs one, stage it (`demo-judge` → STAGE) or one person buys a developer account before the event.

## The demo phone

- Pick **one** phone and **one** Mac for the demo, and do the setup on that pair.
- After every `/hack-promote`, install the promoted build: `scripts/hack run --device --prod`.
- Use the cable. Wireless installs depend on the venue's wifi.
- On stage: phone unlocked, Do Not Disturb on, auto-lock set to Never, brightness up, and the simulator ready on the Mac as the fallback.

## What agents can and can't do

An agent can build, install and launch on the phone, and read the build log. It **cannot see or tap a real phone**. After a phone run it asks you what is on screen. All agent verification happens in the simulator.

## When it fails

`scripts/hack run --device` prints a "→" line for the common causes. The full log is `.hack/state/device-build.log`.

| What you see | Fix |
|---|---|
| "No Account for Team" | Step 1: sign in to Xcode |
| The app installs but won't open: "Untrusted Developer" | Step 6: trust your Apple ID on the phone |
| "Developer Mode disabled" | Step 4 |
| The phone isn't found | Unlock it, re-plug the cable, tap Trust; try another cable (some only charge) |
| "No profiles for …" on the very first build | Open the project in Xcode once with the phone attached, pick your team, build, then `git restore` the project file |
| "maximum number of apps" | Free Apple ID limit: delete an older test app from the phone, keep the same bundle id |
