# aptabase-swift (aukam-dev fork)

aukam.dev's fork of [aptabase/aptabase-swift](https://github.com/aptabase/aptabase-swift), the Swift SDK for
Aptabase analytics. The Nagara iOS app uses it through Swift Package Manager. `README.md` and every file not listed
under "Repository rules" belong to upstream and stay untouched, so upstream updates merge cleanly.

## Status

- `main` mirrors upstream `main`, fast-forward only (synced 2026-10-08 to `90aacf4`, release 0.3.12), plus the
  aukam-dev files listed below.
- The Nagara change lives on the branch `nagara/add-dispose`: commit `9382eba`, "Add dispose() for analytics
  opt-out" (Nagara issue #435), based on upstream 0.3.11. It lets the app shut the SDK down when a user opts out
  of analytics.

## How Nagara uses it

The app pins `https://github.com/aukam-dev/aptabase-swift` at revision `9382eba` (`Nagara.xcodeproj` and
`Package.resolved` in the app repository). That commit must stay reachable: never force-push or delete a
`nagara/*` branch that a released app build pins.

## Syncing with upstream

1. `git fetch upstream` (remote `https://github.com/aptabase/aptabase-swift`).
2. `main`: GitHub's "Sync fork", or
   `gh api -X POST repos/aukam-dev/aptabase-swift/merge-upstream -f branch=main`. Fast-forward only.
3. To move Nagara to a newer upstream release: create `nagara/<topic>` from that release tag, apply the Nagara
   change again (`git cherry-pick 9382eba`), run the package tests, then point the app's pin at the new commit in a
   Nagara pull request.
4. If upstream ships an equivalent of `dispose()`, point Nagara at upstream directly and archive this fork.

## Repository rules

The aukam-dev standard (aukam-dev/repo-template), adapted for a fork:

- Only added files: `FORK.md`, `.editorconfig`, `.githooks/`, `.gitleaks.toml`, `.github/dependabot.yml`,
  `.github/workflows/secret-scan.yml`, and a block appended to `.gitignore`.
- Secret scan: the hooks once per clone (`brew install gitleaks`, `git config core.hooksPath .githooks`), and the
  whole history in CI on every pull request and every push to `main`.
- CI runs on GitHub-hosted runners, free for public repositories. The org's Mac mini runner refuses public
  repositories on purpose: anyone can open a pull request against a public repository.
- Upstream's `.github/workflows/ci.yaml` is disabled in this fork. It needs an action the org does not allow and
  macOS runners this fork does not use.
- Dependabot watches the workflow pins only. The package's own dependencies follow upstream.
- Squash merge only; merged branches are deleted, except `nagara/*` branches, which are never merged into `main`.

## Ownership

Maintained by aukam.dev for the Nagara app. The repository is public because it is a fork of a public repository.
Upstream code is MIT licensed, Copyright (c) 2023 Sumbit Labs Ltd. (see `LICENSE`).
