# The Xcode project: designing out the conflicts

Xcode has three classic merge-conflict sources. Remove all three when the project is created, before lanes start.

## 1. `project.pbxproj`

Every "add file" used to rewrite this file, so two people adding files always conflicted.

- Fastest: `scripts/hack scaffold-ios <AppName>` creates a minimal SwiftUI app already set up this way.
- Or **create the project in Xcode 16 or newer and keep the default folder style** (blue "synchronized" folders). Files are picked up from disk; adding, renaming or deleting a Swift file no longer touches `project.pbxproj`.
- Check: `grep -c PBXFileSystemSynchronizedRootGroup *.xcodeproj/project.pbxproj` prints 1 or more.
- After that, `project.pbxproj` changes only for build settings, capabilities and packages. Those belong to the **lead lane**; everyone else requests them.

## 2. Layout: one folder per lane

```
<App>/
  App/            ← lead lane: @main, root navigation, DI, Info.plist, Assets
  <LaneA>/        ← lane A: everything for its beats
  <LaneB>/
  <LaneC>/
  Shared/         ← lead lane: types every lane uses (the code form of the contracts)
```

Each lane exposes **one entry point** (a root `View`, or a service behind a protocol) named in its `CONTRACT.md`. The lead lane's shell is the only place that references all of them. Lanes never import each other's internals; if two lanes need the same type, it moves to `Shared/` by request to the lead.

Put these folder prefixes in `.hack/lanes.json` → `lanes.<name>.paths` (e.g. `"MyApp/Capture/"`).

## 3. Per-user and generated files

Already in `.gitignore`: `xcuserdata/`, `*.xcuserstate`, `DerivedData/`, `.build/`. Never commit them.

Also owned by the lead lane, change-by-request only: `Assets.xcassets` (or give each lane its own asset catalog inside its folder, which is better), `Info.plist`, entitlements, `Package.resolved`.
