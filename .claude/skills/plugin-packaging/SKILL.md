---
name: plugin-packaging
description: >
  Fixes and modifies how the dota2-coach-plugin wheel/sdist is built (hatchling
  build targets) so the published artifact actually contains the skill payload.
  Use whenever the user reports an "empty wheel", "published wheel is empty",
  "files missing from the package", a skill/doc that "didn't ship", or asks to
  "change hatch build targets", "add a file to the distribution", fix "packaging
  paths", or debug `uv build` output. This repo already shipped an empty wheel once
  because the hatch targets pointed at non-existent root paths — consult this skill
  before editing any `[tool.hatch.build.targets.*]` block.
allowed-tools: Bash(ls*), Bash(grep*), Bash(unzip -l*), Bash(find*)
---

# Building the dota2-coach-plugin distribution

Build backend is **hatchling**; the build command is `uv build` (used by both the CI
`build` job and the release `build` step). UV-only per global CLAUDE.md.

## The bug this skill prevents (empty-wheel, fixed in commit 3b41740)

The payload does NOT live at the repo root. It lives under `plugins/dota2-coach/`.
An earlier `pyproject.toml` pointed hatch at root `skills/`, root `.claude-plugin/`,
and root `.mcp.json` — none of which exist — so the published wheel shipped **zero**
skill files. Any build-target path you write must start with `plugins/dota2-coach/`.

## Correct config (already in pyproject.toml — keep it this shape)

```toml
[tool.hatch.build.targets.wheel]
packages = ["plugins/dota2-coach/skills"]

[tool.hatch.build.targets.sdist]
include = [
    "plugins/dota2-coach/skills/",
    "plugins/dota2-coach/.claude-plugin/",
    "plugins/dota2-coach/.mcp.json",
    "README.md",
    "LICENSE",
]
```

The wheel ships only the `skills` package tree; the sdist additionally ships the
manifest and `.mcp.json`. Expected skill markdown files in the wheel (currently four,
all under `plugins/dota2-coach/skills/dota2-coaching/`):
`SKILL.md`, `ROLE_EXPECTATIONS.md`, `HERO_BENCHMARKS.md`, `DEATH_ANALYSIS.md`.

## Golden rule when adding payload

Adding any new top-level payload dir or file requires editing **both** lists or it
silently ships nothing:
- a new file/dir under `plugins/dota2-coach/skills/` → already covered by the wheel
  `packages` entry and the sdist `skills/` glob (no change needed).
- a brand-new payload location OUTSIDE `plugins/dota2-coach/skills/` (e.g. a top-level
  `agents/` or a new `plugins/dota2-coach/commands/`) → add it to the wheel `packages`
  list AND the sdist `include` list.

## Keep three path-lists aligned

If you ever move the payload, update all three together or release breaks:
1. `pyproject.toml` hatch targets (above).
2. `release.yml` "Validate plugin structure" step — hardcodes `plugins/dota2-coach/...`.
3. `ci.yml` validators ("Validate SKILL.md files", "Validate .mcp.json") — same paths.

## Verifying contents WITHOUT a full build

A python-manta test suite may be running; avoid `uv build` unless the user asks.
Confirm statically:

```
ls plugins/dota2-coach/skills/dota2-coaching/        # expect the 4 .md files
grep -nE 'packages|include' pyproject.toml
```

If a `dist/` already exists from a prior build, inspect it instead of rebuilding:

```
ls -la dist/
unzip -l dist/*.whl        # the listing must show the skills/*.md files
```

An empty/short `unzip -l` listing (no `*.md`) is the empty-wheel regression — the
fix is restoring the `plugins/dota2-coach/skills` path in the wheel target, never a
rebuild flag.
