---
name: hack-init
description: "LEAD, once, before the hackathon: create the team's GitHub repo from this kit with dev and prod branches, labels and auto-merge, and invite the team."
disable-model-invocation: true
---

1. Ask for: how many people are on the team including the user (**2 to 6**), the GitHub org (or user) and repo name, public or private, and each teammate's GitHub username (the handle in github.com/<username>). Then set the size: `scripts/hack team <n>`. One person = one lane, so this is also the number of lanes `/hack-wayfinder` will cut. If the size isn't known yet, leave the default of 4; it can be changed with the same command any time before the lanes are named.
2. Confirm the target out loud (`<org>/<repo>`, visibility), since this creates a repo and pushes to it. On yes: `scripts/hack init <org>/<repo>` (add `--public` if chosen).
   - Creates the repo, pushes `dev` (default branch) and `prod`, turns on auto-merge, creates labels.
   - Tries to protect `dev` (PRs must pass `lane-check`) and `prod` (no force-push). That needs a public repo or a paid org plan; if it can't, shipping still works, PRs just merge without waiting for the check.
3. Invite each teammate with write access: `gh api -X PUT repos/<org>/<repo>/collaborators/<login> -f permission=push`. Tell the user they must accept the invite.
4. **Pick the stack.** Ask: is this an **iOS app** or **something else**? Then `scripts/hack stack ios` or `scripts/hack stack other`, and follow that stack's guide now, before lanes exist, so day one starts at the idea:
   - **iOS** → `stacks/ios/README.md` (*Set up*): scaffold the app, confirm it opens in the simulator. If the demo will run on a real iPhone, also call the Skill tool with "hack-device" now, so the one-time phone setup isn't left for the venue.
   - **Other** → `stacks/other/README.md`: ask what the app is made of (for example "FastAPI + Vite"), then build its start-up process (`scripts/dev`, `run.command`, `run.url`, `run.check`) and write `stacks/other/THIS-APP.md`.
   - Not decided yet → skip; the lead runs `scripts/hack stack <ios|other>` and the guide once it is.
   Either way, finish by confirming `scripts/hack run` launches an empty app.
5. Tell the user what's next: at the hackathon, run `/hack-wayfinder` with the team in the room.
