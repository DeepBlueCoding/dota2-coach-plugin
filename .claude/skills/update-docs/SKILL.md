---
name: update-docs
description: >
  Writes and fixes the user-facing README.md of the dota2-coach-plugin — the repo's
  only prose doc (there is NO mkdocs/docs setup) — keeping it consistent with the real
  manifests, MCP tool surface, and plugin layout. Use whenever the user asks to
  "update the README", "fix the docs", "document a new tool/feature", "fix the install
  command", says the "docs are out of date", or mentions "mkdocs". The README has
  several known-stale spots (wrong org name, wrong plugin structure, drifted tool
  list); consult this skill before editing so you fix them at the source of truth
  rather than copying README into other files.
allowed-tools: Bash(ls*), Bash(grep*), Bash(git remote*)
---

# Maintaining dota2-coach-plugin docs

`README.md` is the SOLE prose doc. There is NO `docs/` directory and NO mkdocs config
anywhere despite any meta-hints. Do NOT scaffold mkdocs or a docs site unless the user
explicitly asks. `ci.yml` has a markdown/ruff lint step but it ends in `|| true`
(non-blocking), so doc errors never fail CI — accuracy is on you, check it manually.

## Known-stale spots in README.md (fix these whenever you touch it)

1. **Install command + contributing link use the wrong org.** README says
   `/plugin install equilibrium-coach/dota2-coach-plugin` and links
   `github.com/equilibrium-coach/...`. The real repo is
   **`github.com/DeepBlueCoding/dota2-coach-plugin`** (git remote + `pyproject.toml`
   `[project.urls]`). The marketplace name is **`deepbluecoding`**
   (`.claude-plugin/marketplace.json`). Align all README org/repo references to
   DeepBlueCoding.

2. **"Plugin Structure" section shows the wrong layout.** README draws root-level
   `.mcp.json` and `skills/`. The real layout is nested under `plugins/dota2-coach/`:
   ```
   dota2-coach-plugin/
   ├── plugins/dota2-coach/
   │   ├── .claude-plugin/plugin.json
   │   ├── .mcp.json
   │   └── skills/dota2-coaching/{SKILL.md,ROLE_EXPECTATIONS.md,HERO_BENCHMARKS.md,DEATH_ANALYSIS.md}
   ├── .claude-plugin/{plugin.json,marketplace.json}
   ├── pyproject.toml
   └── README.md
   ```

3. **Tool catalog drifts from SKILL.md.** README lists `search_pro_player`,
   `get_pro_matches`, `get_team_matches`; `dota2-coaching/SKILL.md` lists
   `get_hero_positions`, `get_snapshot_at_time` instead. Reconcile **both docs against
   the pinned `mcp-replay-dota2` server tool set** (see the `mcp-server-pin` skill),
   NOT against each other — neither doc is authoritative.

4. **Author/identity drift across manifests.** `.claude-plugin/plugin.json` and
   `plugins/dota2-coach/.claude-plugin/plugin.json` both say author "Equilibrium Coach"
   / repo "equilibrium-coach", while `marketplace.json` and `pyproject.toml` use
   "DeepBlueCoding". When doc-fixing, align author/repository to **DeepBlueCoding**.

## Sources of truth (reconcile README against these, never the reverse)

```
git remote -v                                            # real repo URL
grep -n urls -A4 pyproject.toml                          # Homepage/Repository
grep -n name .claude-plugin/marketplace.json             # marketplace name = deepbluecoding
grep -n 'get_' plugins/dota2-coach/skills/dota2-coaching/SKILL.md   # current tool list
cat plugins/dota2-coach/.mcp.json                        # the pinned server providing tools
```

- Install/marketplace identity → `marketplace.json` + git remote.
- MCP tool names → the pinned `mcp-replay-dota2` server (the `mcp-server-pin` skill).
- Plugin layout → the actual `plugins/dota2-coach/` tree (`ls`), not the old README.

Verify everything with `ls`/`grep` — do not run builds or the test suite to check docs.
