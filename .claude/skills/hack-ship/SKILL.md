---
name: hack-ship
description: Ship the current lane's work into dev in one step (commit, pull teammates' work, open the PR, merge it). Use when the user says ship, push, merge, PR, send it, or when a ticket's work is done and has been run.
---

```bash
scripts/hack ship "<what a judge now sees, in a few words>" --closes <ticket-number>
```

What it does, in order: stages everything → lane check (aborts if anything is outside your lane) → commits → merges the latest `dev` into your branch → runs `run.check` from `.hack/lanes.json` if one is set → pushes `lane/<you>` → opens a PR into `dev` → merges it (auto-merge once the `lane-check` passes, or immediately where the repo has no required checks).

Rules:

- Ship when it **runs**, not when it's finished. Small and often.
- Never `git push` by hand, never push to `dev` or `prod`, never force. The guard blocks these.
- If the lane check fails, the listed files are not yours. Move the change into your lane or `scripts/hack request` it, then ship again.
- If sync reports a conflict, stop and tell the user: a conflict means two lanes touched one file, and the owning lane's version wins.
- If the PR opens but doesn't merge, run `gh pr checks <n>` and report what failed.

After a successful ship, say in one line what is now in dev.
