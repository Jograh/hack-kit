# Stacks

A stack is the kind of app the team is building. It decides how the app is created, run and tested. The lead picks one during `/hack-init`:

```bash
scripts/hack stack ios      # or: other
```

The choice is stored as `stack` in `.hack/lanes.json`. Agents: read `stacks/<stack>/README.md` before you create the project, change how it runs, or answer "how do I test this?".

| Stack | Use it when | Guide |
|---|---|---|
| `ios` | A native iPhone app in Swift / SwiftUI | [ios/README.md](ios/README.md) |
| `other` | Anything else: a web app, an API plus a front end, a CLI, a bot | [other/README.md](other/README.md) |

Whatever the stack, everyone uses the same words: "run it" (`scripts/hack run`) and "ship it". The stack only changes what happens underneath.

This folder belongs to the lead lane.
