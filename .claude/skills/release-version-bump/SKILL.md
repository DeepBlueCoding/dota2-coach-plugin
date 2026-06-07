---
name: release-version-bump
description: >
  Cuts a release of the dota2-coach-plugin and bumps its version across every
  version-bearing file so CI and the release workflow both pass. Use whenever the
  user asks to "release a version", "cut a release", "bump the version", "publish
  to PyPI/TestPyPI", "tag a new version", "make a release", or when a CI
  "version mismatch" / "Verify version matches tag" job fails. This repo keeps the
  version in FOUR files but commitizen only updates TWO, and release.yml checks a
  stale file, so a naive bump silently breaks the release — use this skill even if
  the bump looks like a one-line edit.
allowed-tools: Bash(uv run cz*), Bash(git tag*), Bash(git push*), Bash(grep*), Bash(git log*), Bash(git status*), Bash(git diff*)
---

# Releasing / version-bumping dota2-coach-plugin

Repo conventions (branch off `main`, commit/push only when asked, UV-only) live in
the global CLAUDE.md — follow them. This skill adds the release mechanics that file lacks.

## The version-drift map (the whole reason this skill exists)

The version string lives in FOUR places. `commitizen` (`version_files` in `pyproject.toml`)
updates only the first two. The last two you MUST edit by hand:

| File | Field | Owner | Current |
|------|-------|-------|---------|
| `pyproject.toml` | `[project] version` + `[tool.commitizen] version` | commitizen (auto) | 1.0.9 |
| `plugins/dota2-coach/.claude-plugin/plugin.json` | `version` | commitizen (auto) | 1.0.9 |
| `.claude-plugin/plugin.json` (ROOT) | `version` | **manual** | 1.1.0.dev1 (DRIFTED) |
| `.claude-plugin/marketplace.json` | `metadata.version` AND `plugins[0].version` (TWO spots) | **manual** | 1.0.0 (DRIFTED) |

The root `plugin.json` and `marketplace.json` are already out of sync with the real
version. After bumping, set ALL four files (five JSON values) to the same new version.

## Two CI/release facts you must know before tagging

1. **CI** (`.github/workflows/ci.yml`, "Check version consistency") only compares
   `pyproject.toml` vs `plugins/dota2-coach/.claude-plugin/plugin.json`. So CI passes
   even when the root file and marketplace.json are stale.
2. **release.yml LATENT BUG** (`.github/workflows/release.yml` line ~34): its
   "Verify version matches tag" step reads `.claude-plugin/plugin.json` — the **ROOT**
   (stale) file — not the `plugins/` one. So a clean commitizen bump still fails the
   release job until the root file equals the tag. The correct long-term fix is to
   change that line to read `plugins/dota2-coach/.claude-plugin/plugin.json` (matching
   CI). If the user is doing a real release now and you cannot touch the workflow,
   you MUST bump the root file by hand to the new version regardless.

## Release workflow

1. Confirm you are on `main` (or a release branch off main) and the tree is clean:
   `git status`.
2. Bump with commitizen — it reads conventional commits, picks the next version,
   updates the two tracked files, writes `CHANGELOG.md`, and creates the bump commit:
   ```
   uv run cz bump
   ```
   - tag_format is `v$version`; bump_message is `bump: version $current_version → $new_version`.
   - `update_changelog_on_bump=true`, `changelog_incremental=true` → appends to `CHANGELOG.md`.
   - To force a level: `uv run cz bump --increment PATCH|MINOR|MAJOR`.
   - For a prerelease: `uv run cz bump --prerelease alpha|beta|rc` (PEP 440 `aN/bN/rcN`).
3. Read the new version commitizen chose (from its output or `uv run cz version --project`).
4. **Hand-fix the two drifted files** to that exact version:
   - `.claude-plugin/plugin.json` → `version`
   - `.claude-plugin/marketplace.json` → `metadata.version` AND `plugins[0].version`
5. Run the pre-tag checklist below. Fix anything red BEFORE tagging.
6. Tag and push (only when the user asks to publish). `cz bump` does not push:
   ```
   git push origin main
   git push origin v<new-version>
   ```
   Pushing the `v*` tag triggers `release.yml`.

## Pre-tag checklist (mirrors CI + release.yml — verify statically, do NOT build)

A python-manta test suite may be running on this machine. Do NOT run `uv build` or
re-bump just to "verify". Confirm with grep instead:

```
grep -n '"version"' .claude-plugin/plugin.json .claude-plugin/marketplace.json plugins/dota2-coach/.claude-plugin/plugin.json
grep -nE '^version|^version =' pyproject.toml
```

- [ ] All FIVE version values (4 files; marketplace counts twice) equal the new version.
- [ ] `pyproject.toml [project].version` == `plugins/dota2-coach/.claude-plugin/plugin.json` (CI gate).
- [ ] Root `.claude-plugin/plugin.json` == new version (release.yml gate — the latent-bug file).
- [ ] Tag you will push is exactly `v<new-version>` (release.yml strips the leading `v`).
- [ ] `CHANGELOG.md` got the new entry.

## Tag → publish routing (release.yml `check-version` job)

The regex `(\.dev|a[0-9]|b[0-9]|rc[0-9])` on the tag decides the route:
- **Prerelease** (`1.2.0.dev1`, `1.2.0a1`, `1.2.0b1`, `1.2.0rc1`) → publishes to **TestPyPI** only.
- **Stable** (`1.2.0`) → publishes to **PyPI** + creates a softprops **GitHub release**.

So if the user wants a real PyPI release, the version must NOT contain `.dev/aN/bN/rcN`.
