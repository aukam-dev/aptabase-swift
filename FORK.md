# aptabase-swift (aukam-dev fork)

aukam.dev's fork of [aptabase/aptabase-swift](https://github.com/aptabase/aptabase-swift), the Swift SDK for
Aptabase analytics. The Nagara iOS app uses it through Swift Package Manager. `README.md` and every file not listed
under "Repository rules" belong to upstream and stay untouched, so upstream updates merge cleanly. The single
exception is the `dispose()` addition, which lives inside upstream's own files and is listed there file by file.

## Status

- `main` is upstream `main` (synced 2026-10-08 to `90aacf4`, release 0.3.12), plus the aukam-dev files and the
  `dispose()` addition listed below. Never force-pushed, never rewritten.
- `dispose()` is on `main` since the 2026-W44 housekeeping change; before that it existed only on the branch
  `nagara/add-dispose`. It lets the app shut the SDK down when a user opts out of analytics.
- That branch stays because a released app build pins its commit: `9382eba`, "Add dispose() for analytics
  opt-out" (Nagara issue #435), based on upstream 0.3.11.

## How Nagara uses it

The app pins `https://github.com/aukam-dev/aptabase-swift` at revision `9382eba` (`Nagara.xcodeproj` and
`Package.resolved` in the app repository). That commit must stay reachable: never force-push or delete a
`nagara/*` branch that a released app build pins.

## Syncing with upstream

1. `git fetch upstream` (remote `https://github.com/aptabase/aptabase-swift`).
2. `main`: GitHub's "Sync fork", or
   `gh api -X POST repos/aukam-dev/aptabase-swift/merge-upstream -f branch=main`. This is a merge, not a
   fast-forward: `main` carries local commits on top of the upstream release it was synced to. An upstream change
   to one of the six `dispose()` files listed below can conflict; keep both sides and run `swift test`.
3. To move Nagara to a newer upstream release: sync `main` as above, run `swift test`, then point the app's pin
   at the new `main` commit in a Nagara pull request. (While `dispose()` was branch-only this meant a fresh
   `nagara/<topic>` from the release tag and a `git cherry-pick 9382eba`; that detour is gone.)
4. If upstream ships an equivalent of `dispose()`, point Nagara at upstream directly and archive this fork.

## Repository rules

The aukam-dev standard (aukam-dev/repo-template), adapted for a fork:

- Only added files: `FORK.md`, `.editorconfig`, `.githooks/`, `.gitleaks.toml`, `.github/dependabot.yml`,
  `.github/workflows/secret-scan.yml`, and a block appended to `.gitignore`.
- Only modified upstream code files (besides the `.gitignore` block above), all of them `dispose()` and all of
  them additive, so upstream behaviour is unchanged: `Sources/Aptabase/` `Aptabase.swift`, `AptabaseClient.swift`,
  `ConcurrentQueue.swift`, `EventDispatcher.swift`, and `Tests/AptabaseTests/` `ConcurrentQueueTests.swift`,
  `EventDispatcherTests.swift`. Nothing else under `Sources` or `Tests` diverges from upstream.
- Secret scan: the hooks once per clone (`brew install gitleaks`, `git config core.hooksPath .githooks`), and the
  whole history in CI on every pull request and every push to `main`.
- CI runs on GitHub-hosted runners, free for public repositories. The org's Mac mini runner refuses public
  repositories on purpose: anyone can open a pull request against a public repository.
- Upstream's `.github/workflows/ci.yaml` is disabled in this fork. It needs an action the org does not allow and
  macOS runners this fork does not use. So a pull request here is checked by the secret scan only: the test
  evidence a change carries is a local `swift test`, named in the pull request.
- Dependabot watches the workflow pins only. The package's own dependencies follow upstream.
- Squash merge only; merged branches are deleted, except `nagara/*` branches, which are never merged into `main`.

## Ownership

Maintained by aukam.dev for the Nagara app. The repository is public because it is a fork of a public repository.
Upstream code is MIT licensed, Copyright (c) 2023 Sumbit Labs Ltd. (see `LICENSE`).
