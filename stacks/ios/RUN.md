# Running in the simulator

```bash
scripts/hack run            # opens the Simulator window
scripts/hack run --panel    # for Claude's built-in simulator panel (the hack-run skill uses this)
scripts/hack sim            # just boot the simulator and print its id
```

What `run` does: finds the project and scheme, boots an iPhone simulator, builds, installs, launches, and saves a screenshot to `.hack/state/last-run.png`.

- **Build output lives outside the repo**, in `~/Library/Caches/hack-kit/`. Folders synced by iCloud (Desktop, Documents) add file attributes that make code signing fail.
- **Which simulator:** the one named in `.hack/lanes.json` → `run.simulator`, else an already-booted standard iPhone, else the first available iPhone.
- **Which scheme:** `run.scheme`, else the project's first scheme.
- **Build failed:** the errors are printed; the full log is `.hack/state/build.log`.

Agents verify in the simulator: take a screenshot, tap through the flow, and take another. A screenshot taken straight after a tap can catch the screen mid-animation; take it again before deciding something failed.
