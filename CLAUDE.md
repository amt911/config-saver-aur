# config-saver-aur — Claude Guide

Arch Linux (AUR-style) packaging repo for **`config-saver`**, a Python utility to back up and restore configuration files. This repo is the *package recipe*, not the application source — the app lives at `https://github.com/amt911/config-saver` and is pulled in as a release tarball at build time.

## ⚡ superpowers — use whenever applicable

Always prefer **superpowers** skills over ad-hoc approaches. If there's even a small chance a skill applies to the task, invoke it via the `Skill` tool before acting (including before clarifying questions).

- **Process skills first** — `brainstorming` before creative/feature work, `systematic-debugging` before fixing bugs, `verification-before-completion` before claiming done.
- **Then implementation skills** — domain-specific skills guide execution.
- **Verify before claiming done** — `verification-before-completion` / `requesting-code-review` before merging.

User instructions always take precedence over skills; skills override default behavior.

### Mode switch

- **"lite mode"** — fully disables superpowers: no skill is invoked, not even the applicability check, until **"normal mode"** is said.
- **"normal mode"** (default) — standard superpowers behavior, plus: when delegating coding work, dispatch at most 1 agent at a time, and never use a model above Sonnet (no Opus).
- **"modo desatendido"** (unattended mode) — the user is away and delegates autonomy: work without waiting for confirmations and make reasonable decisions yourself instead of asking. In this mode you MAY **`git push` the feature branches you create** and **open PRs via `gh`** on your own, so the work is ready for review when the user returns. The hard limits still hold and are NOT lifted: **never merge anything** (no `git merge`, no fast-forward integration, no `gh pr merge`), **never push to `main`** or any protected/default branch directly, and **never** `git push --force` / `--force-with-lease`. Deliver everything as pushed branches + PRs for the user to merge. Reverts to defaults on **"normal mode"**.

Confirm the switch briefly when it happens.

## Stack

- **Arch Linux packaging (`makepkg` / PKGBUILD)** — the whole repo is one `PKGBUILD` plus `.SRCINFO` and `README.md`.
- **Bash** — `PKGBUILD` is a bash script with `build()` / `package()` functions; `arch=('any')` (architecture-independent).
- **Upstream: Python** — built as a wheel via `python-build` + `python-installer`; runtime deps: `python`, `python-pydantic`, `python-colorama`, `python-tqdm`, `python-yaml`, `python-rich`.

### What the package installs

- The `config-saver` wheel (into `$pkgdir` via `python -m installer`).
- `README.md` → `/usr/share/doc/config-saver/`.
- Bundled config files → `/etc/config-saver/configs/` (644 files, 755 dirs).
- systemd unit + timer → `/usr/lib/systemd/system/config-saver@.{service,timer}`.

## Commands

```bash
# build + install locally (also runs deps resolution)
makepkg -si

# build only (clean each time), force overwrite of existing package
makepkg -f

# refresh sha256sums in PKGBUILD after a version bump
updpkgsums

# regenerate .SRCINFO from PKGBUILD (must be committed alongside PKGBUILD changes)
makepkg --printsrcinfo > .SRCINFO

# lint the recipe and the built package
namcap PKGBUILD
namcap config-saver-*-any.pkg.tar.zst

# build in a clean chroot (catches missing deps / dirty-env bugs)
extra-x86_64-build          # or: makechrootpkg -c -r <chroot>
```

Build artifacts (`src/`, `pkg/`, `*.pkg.tar.*`, tarballs) are git-ignored — only `PKGBUILD`, `.SRCINFO`, `README.md`, `.gitignore` are tracked.

## Packaging quality

There are no unit tests here — quality for a packaging repo means the recipe is correct, reproducible, and lints clean. Treat these as the gates:

- **`namcap` is clean** — run `namcap PKGBUILD` and `namcap` on the built `.pkg.tar.zst`. Resolve real warnings (missing/excess deps, wrong permissions, unstripped binaries). Justify any intentionally-ignored warning.
- **`.SRCINFO` stays in sync with `PKGBUILD`** — any edit to `PKGBUILD` (version, deps, sources) requires regenerating `.SRCINFO` (`makepkg --printsrcinfo > .SRCINFO`) and committing both together. An out-of-sync `.SRCINFO` is the most common AUR breakage. Note: `.SRCINFO` is currently **not** tracked in this repo — if you touch packaging metadata, add it.
- **Checksums updated** — after bumping `pkgver` or changing `source`, run `updpkgsums` so `sha256sums` matches the new tarball. Never hand-edit a checksum; never leave `'SKIP'` for a release tarball.
- **Version bumps are correct** — `pkgver` matches the real upstream tag; reset `pkgrel=1` on a new `pkgver`, and only increment `pkgrel` when repackaging the *same* upstream version. Confirm the upstream tag/tarball actually exists before bumping.
- **Builds in a clean chroot** — the package must build with only its declared `depends`/`makedepends` present. A build that only works on your dev box (extra tools installed globally) is a bug — verify with `extra-x86_64-build` or `makechrootpkg`.
- **Correct dep split** — runtime libraries in `depends`, build-only tooling (`python-build`, `python-installer`, `python-wheel`, `git`) in `makedepends`. Don't ship makedepends as runtime deps or vice versa.

**Manual verification is the real test** — the acceptance criterion for a packaging change is: it builds, installs, and the installed `config-saver --help` runs and the systemd units are present. Do that yourself before claiming a change works; don't infer success from a green `makepkg` alone.

## Working rules

- **Use superpowers skills whenever they apply** — invoke via `Skill` before acting; process skills before implementation skills.
- **Keep `.SRCINFO` and checksums in lockstep with `PKGBUILD`** — regenerate both after any metadata change; never commit a `PKGBUILD` edit without them.
- **Don't add dependencies casually** — the `depends`/`makedepends` lists are intentional and mirror the upstream requirements. Add one only when the package genuinely needs it, and prefer official-repo package names.
- **Don't hand-edit generated fields** — `sha256sums` come from `updpkgsums`, `.SRCINFO` from `makepkg --printsrcinfo`. Regenerate, don't type.
- **Lint before proposing done** — `namcap` on both `PKGBUILD` and the built package.
- **Don't commit build artifacts** — respect `.gitignore`; only the recipe files belong in git.

## Git & GitHub

- **Commits and branches OK** — create commits and new branches whenever it makes sense, without asking first.
- **Never push** *(default)* — no `git push` under any circumstance, and absolutely never `git push --force` / `--force-with-lease`. Leave pushing to the user. **Exception:** when **"modo desatendido"** is active, you may push the feature branches you create (never `main`/protected branches, never force) so PRs are ready for review.
- **Never merge — no permission** — you do NOT have permission to merge anything into any branch, nor to merge any pull request. No `git merge`, no fast-forward integration, no `gh pr merge`. Leave every merge (branches and PRs alike) to the user. This holds in every mode, **including "modo desatendido"**.
- **GitHub via `gh`** — if the `gh` CLI is available, you may open pull requests, issues, and similar (comments, labels, etc.). These don't require pushing on your part beyond what `gh` itself does for an already-pushed branch.
- **Every PR must include a manual test plan** — when opening a PR, add a **How to test manually** section describing the exact steps to exercise the change by hand. For this repo, "manual test" = build and install the package and verify it works: run `makepkg -f` (and `makepkg -si` where practical) from a clean tree, confirm `namcap` is clean, then check that `config-saver --help` runs and the installed files (`/etc/config-saver/configs/`, the systemd unit + timer, the doc) landed where the `package()` function puts them. Note any setup (e.g. upstream tag must exist) and edge cases (fresh chroot, version bump correctness).
