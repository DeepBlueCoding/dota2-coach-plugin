---
name: authoring-coaching-skill
description: >
  Adds or edits the DISTRIBUTED Dota 2 coaching skill content that ships to plugin
  consumers — the SKILL.md and its supporting markdown under
  plugins/dota2-coach/skills/ — in the exact format the CI validator enforces. Use
  whenever the user asks to "edit the coaching skill", "add a coaching framework",
  "update SKILL.md", "add a benchmark/role/death doc", "add a new skill to the
  plugin", or mentions the plugin's "skill frontmatter". This is PRODUCT content
  (instructions shipped to the model on consumers' machines), distinct from this
  repo's own development skills in .claude/skills/.
allowed-tools: Bash(ls*), Bash(grep*), Bash(cat*)
---

# Authoring the distributed coaching skill

These are the skills consumers receive, NOT this repo's dev tooling. They live at:

```
plugins/dota2-coach/skills/<skill-name>/SKILL.md
plugins/dota2-coach/skills/<skill-name>/<SUPPORTING>.md   (optional, beside SKILL.md)
```

The only existing skill is `dota2-coaching`. Its supporting docs sit beside SKILL.md:
`ROLE_EXPECTATIONS.md`, `HERO_BENCHMARKS.md`, `DEATH_ANALYSIS.md`, referenced with
relative markdown links like `[ROLE_EXPECTATIONS.md](ROLE_EXPECTATIONS.md)`.

## CI frontmatter contract (hard-fails the build — get this exact)

`ci.yml` "Validate SKILL.md files" iterates EVERY subdir of
`plugins/dota2-coach/skills/` and requires each to contain a `SKILL.md` that:
1. **starts with** `---` (frontmatter opener; no blank line or BOM before it),
2. matches the regex `^---\n(.*?)\n---` (a closed `---` block), and
3. has a frontmatter containing both `name:` and `description:`.

No other field is required or validated. `release.yml` also lists every skill subdir
and expects a `SKILL.md` in each. So: never create a subdir under `skills/` without a
valid `SKILL.md`, or both workflows fail.

## Frontmatter pattern to copy (so Claude auto-loads it)

The existing skill uses a multi-line description with explicit `Use when:` and
`Triggers on:` lines. Mirror that — pack the WHAT plus the literal user phrasings that
should fire it:

```yaml
---
name: <kebab-case, matches the dir name>
description: |
  <one sentence: what this coaching framework does>.
  Use when: <trigger situations>.
  Triggers on: "<phrase a user types>", "<phrase>", "<phrase>".
---
```

`name` must be kebab-case and match the directory name (the dir is what consumers type
as `/<name>`). Keep `name`/`description` lowercase-safe and free of XML tags.

## Adding a NEW distributed skill

1. `mkdir -p plugins/dota2-coach/skills/<new-name>/` and write `SKILL.md` with the
   frontmatter contract above.
2. Put heavy reference material in sibling `.md` files (one level deep) and link them
   from SKILL.md with relative links — keep the SKILL.md body lean.
3. No `pyproject.toml` change is needed for a new skill DIR: the hatch wheel target
   `packages = ["plugins/dota2-coach/skills"]` auto-packages every subdir. (A
   brand-new payload location OUTSIDE `skills/` WOULD need packaging changes — see the
   `plugin-packaging` skill.)
4. Verify statically (do NOT build during a python-manta test run):
   ```
   ls plugins/dota2-coach/skills/<new-name>/SKILL.md
   head -1 plugins/dota2-coach/skills/<new-name>/SKILL.md   # must be ---
   ```

## MCP tool list must stay aligned

The "MCP Tools Reference" section in `dota2-coaching/SKILL.md` lists `get_*` tools that
belong to the pinned `mcp-replay-dota2` server (see the `mcp-server-pin` skill), NOT
this repo. Two drifts exist today, reconcile against the SERVER (not against each other):
- SKILL.md lists `get_hero_positions` and `get_snapshot_at_time` that README omits.
- README lists `search_pro_player`, `get_pro_matches`, `get_team_matches` that SKILL.md
  omits.
When you edit the tool list, verify each name against the pinned server version, then
update SKILL.md and README together (see the `update-docs` skill).
