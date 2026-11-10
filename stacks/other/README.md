# Other stacks

For anything that isn't a native iOS app. There is no fixed recipe here: **the lead's agent builds the start-up process for this particular app once**, and wires it into the same commands everyone already uses. After that, "run it" works the same for the whole team.

## What to build (lead lane, during `/hack-init`, before lanes exist)

1. `scripts/hack stack other`
2. Scaffold the app the way its ecosystem expects (for example `npm create vite@latest`, `uv init` + FastAPI). Keep dependency manifests and lockfiles at the root or in the lead lane: they are where merge conflicts come from.
3. Write **one start script**, `scripts/dev`, that brings up everything the app needs and then returns:
   - starts each server in the background and writes its output to `.hack/state/<name>.log` and its process id to `.hack/state/<name>.pid`;
   - stops whatever a previous run left behind first, so running it twice is safe;
   - waits until each service answers (poll a health URL or the port) and fails loudly with the last log lines if one doesn't;
   - prints the URL to open.
4. Point the kit at it in `.hack/lanes.json`:

   ```json
   "run": { "command": "scripts/dev", "url": "http://localhost:5173", "check": "<a fast command that fails when the app is broken>" }
   ```

   - `command`: what `scripts/hack run` executes.
   - `url`: what the agent opens in the browser pane afterwards to look at the result.
   - `check`: run by `scripts/hack ship` before every merge. Keep it under ~20 seconds (a type check, a build, one smoke request), or people will stop shipping.
5. Run `scripts/hack run`, open the URL in the browser pane, and confirm an empty app shows. Then ship.
6. Write what you built into `stacks/other/THIS-APP.md`: the services, their ports, the env vars they need (names only; values go in `.env`), where the logs are, and how to reset state. Every agent reads that file when asked "how do I run or test this?".

## Example: FastAPI + Vite

`scripts/dev` would stop anything on ports 8000 and 5173, start `uvicorn app.main:app --reload --port 8000` and `npm run dev -- --port 5173` in the background with logs under `.hack/state/`, wait for `GET http://localhost:8000/health` and `http://localhost:5173` to answer, then print the front-end URL. `run.url` is `http://localhost:5173`; `run.check` is `npm run build && python -m compileall -q app`.

## How it connects to the existing skills

| Skill | What it does on this stack |
|---|---|
| `hack-run` | `scripts/hack run`, then opens `run.url` in the browser pane and looks at the page |
| `hack-ship` | runs `run.check` before merging |
| `hack-orchestrate` | launches the app after a visible ticket lands, same as on iOS; workers still never start servers |
| `/hack-promote` | performs the golden path against the running app |
| `/hack-wayfinder` | cuts lanes by folder; put each service's routes or pages in per-lane folders, with one lead-owned file that mounts them all |

## Conflict traps to design out now

- **One file that registers everything** (a router index, an app shell). Lead lane owns it; lanes expose one entry point each and ask the lead to mount it.
- **Manifests and lockfiles** (`package.json`, `pyproject.toml`, lockfiles). Lead lane only; a new dependency is a request.
- **Database migrations.** Number them by timestamp, never by sequence, or two lanes will both create `0004`.
- **Generated files** (build output, caches, `.env`). Add them to `.gitignore` before the first ship.
- **Ports.** Fix them in `scripts/dev`; don't let tools pick a free one, or `run.url` will be wrong.

If the demo needs to be reachable from a judge's phone or another machine, decide now whether that is a deploy or a tunnel, and make it one more step in `scripts/dev` or a separate `scripts/demo`.
