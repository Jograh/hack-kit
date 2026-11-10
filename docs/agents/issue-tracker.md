# Issue tracker: GitHub, with lanes

Tickets, requests and the map live as GitHub issues in this repo. Use the `gh` CLI. This file is what skills mean by "the issue tracker".

## Labels

| Label | Meaning |
|-------|---------|
| `lane:<name>` | The one lane that owns this ticket. Every ticket has exactly one. |
| `ready-for-agent` | Fully specified: the orchestrator hands it to a background worker. |
| `needs-human` | A person must decide or act. Never dispatched; surfaced to the user. |
| `spec` | A lane's design, written by `/hack-spec`. Not built and not in the queue; `/hack-tickets` splits it into tickets and closes it. |
| `request` | A cross-lane ask. Created with `scripts/hack request`. |
| `broadcast` | Every lane's agent is shown activity on this issue. |
| `hack:map` | The map: one issue, the index of the whole effort. |
| `P0-golden-path` / `P1-real` / `P2-polish` | Priority: a demo beat working for real / holds up when a judge goes off script / polish on the showcase features. |

There is no separate triage step: whoever writes a ticket gives it `ready-for-agent` or `needs-human`. `scripts/hack queue` shows a lane's tickets grouped that way.

## Operations

- **Create a ticket**: `gh issue create --title "[<lane>] ..." --label "lane:<lane>" --label P0-golden-path --body "..."` (heredoc for the body).
- **Cross-lane request**: `scripts/hack request <lane> "title" "details"`.
- **My lane's open tickets**: `scripts/hack inbox --all`.
- **Read**: `gh issue view <n> --comments`.
- **Claim**: `gh issue edit <n> --add-assignee @me`, before any work. Unassigned = unclaimed.
- **Comment / reply**: `gh issue comment <n> --body "..."`.
- **Close**: shipping with `scripts/hack ship "msg" --closes <n>` closes it when the PR merges (`dev` is the default branch). To close without code: comment why, then `gh issue close <n>`.
- **Broadcast to everyone**: comment on the map issue (`gh issue list --label hack:map`).
- **Sub-issue of the map**: put `Part of #<map>` on the first line of the body, and list it in the map's checklist. (Use native sub-issues if your `gh` supports `--parent`.)
- **Blocking**: a `Blocked by: #<n>` line in the body, same lane only. Across lanes there is no blocking: build against the other lane's CONTRACT stub.

## When a skill says "publish to the issue tracker"

Create a GitHub issue with exactly one `lane:` label.

## When a skill says "fetch the relevant ticket"

`gh issue view <n> --comments`.

## The map

One issue labelled `hack:map` + `broadcast`, titled `MAP: <project>`. Body: Destination (links `docs/DEMO.md`), the lane table, Decisions so far, Not yet specified, Cut list, and a ticket checklist per lane. It is an index: it links to tickets and docs, it never restates them.
