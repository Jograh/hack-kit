# iOS

Everything specific to building an iPhone app with this kit.

| File | Read it when |
|---|---|
| [PROJECT.md](PROJECT.md) | Creating the Xcode project, or changing its layout. How it is set up so that adding files never causes a merge conflict. |
| [RUN.md](RUN.md) | Running the app in the simulator, in Claude's panel or from a terminal. |
| [DEVICE.md](DEVICE.md) | Putting the app on a real iPhone over a cable, for testing and for the demo. |
| `project.pbxproj.template` | Used by `scripts/hack scaffold-ios`. Don't edit by hand. |

## Set up (lead, once, during `/hack-init`)

1. `scripts/hack stack ios`
2. `scripts/hack scaffold-ios <AppName>` creates `<AppName>.xcodeproj` and `<AppName>/App/` + `<AppName>/Shared/`. The name is letters and digits only.
3. `scripts/hack run` and confirm the empty app opens in the simulator.
4. Ship it. `/hack-wayfinder` later adds one folder per lane under `<AppName>/`.

## Day to day

| You say | What runs | Where it shows |
|---|---|---|
| "run it" | `scripts/hack run --panel` | The simulator panel beside the chat. Agents verify here. |
| "put it on my phone" | `scripts/hack run --device` | Your iPhone. Only you can see it. |
| "put the demo build on the phone" | `scripts/hack run --device --prod` | The demo iPhone, built from `prod`. |

## No Mac?

You can write Swift in your lane and ship. Nothing compiles your code until a Mac pulls it, so pair with a Mac lane that runs the app after each of your ships, or take a lane that isn't Swift (backend, data, prompts, the pitch).
