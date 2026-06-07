---
name: mcp-server-pin
description: >
  Updates or troubleshoots the bundled mcp-replay-dota2 MCP server for the
  dota2-coach-plugin — its version pin, uvx launch command, env block, and the
  get_* replay-analysis tool surface that consumers see. Use whenever the user asks
  to "bump mcp-replay-dota2", "update the MCP server version", change the "MCP pin",
  "add an MCP tool", fix "MCP server won't start", reports a "stale MCP pin", or asks
  about the "uvx command for the server". The runtime pin and the pyproject extra
  drift independently and CI does not validate the pin, so always check this skill
  before editing the pin.
allowed-tools: Bash(grep*), Bash(uvx*), Bash(curl -s*)
---

# Managing the mcp-replay-dota2 MCP server

## The two independent places the version appears

1. **RUNTIME pin (what consumers actually run)** —
   `plugins/dota2-coach/.mcp.json`:
   ```json
   {
     "dota2-match-analysis": {
       "command": "uvx",
       "args": ["--from", "mcp-replay-dota2==1.2.0", "dota-match-mcp-server"],
       "env": {}
     }
   }
   ```
   The `==1.2.0` `--from` pin is the effective version. The server key is
   `dota2-match-analysis`; the console entry point is `dota-match-mcp-server`. The
   server is launched **on demand by uvx**, not pip-installed — do NOT add it as a
   hard project dependency.

2. **Dev/extra constraint (softer, NOT the runtime pin)** —
   `pyproject.toml [project.optional-dependencies] mcp = ["mcp-replay-dota2>=1.0.4"]`.
   This is a lower bound for the optional `mcp` extra and drifts independently of the
   runtime pin (today: `.mcp.json`==1.2.0 while pyproject==`>=1.0.4`).

## Bumping the pin

1. Check the current PyPI release before pinning:
   ```
   curl -s https://pypi.org/pypi/mcp-replay-dota2/json | python3 -c 'import json,sys; print(json.load(sys.stdin)["info"]["version"])'
   ```
   (or `uvx --from mcp-replay-dota2 dota-match-mcp-server --help` to probe, but that
   downloads the package — avoid heavy ops while the python-manta test suite runs.)
2. Update the `==` pin in `plugins/dota2-coach/.mcp.json` to that release. Repo intent
   is to keep this `current`.
3. Bump the pyproject `>=` lower bound ONLY if the new release introduces a minimum
   you actually require; otherwise leave it.
4. Keep `command: "uvx"` and `env: {}` unless the server documents required env vars.

## CI does NOT protect the pin

`ci.yml` "Validate .mcp.json" only asserts each server entry has a `command` key. It
never checks the version, so a stale or wrong pin passes CI silently. Verify the pin
by hand:
```
grep -n 'mcp-replay-dota2' plugins/dota2-coach/.mcp.json pyproject.toml
```

## Tool surface lives in the server, not this repo

The `get_*` tools the coaching skill references are defined by `mcp-replay-dota2`, NOT
in this repo. Currently referenced in `plugins/dota2-coach/skills/dota2-coaching/SKILL.md`:
`get_match_info`, `get_draft`, `get_hero_deaths`, `get_death_details`,
`get_farming_pattern`, `get_cs_at_minute`, `get_fights`, `get_fight_details`,
`get_timeline`, `get_objective_kills`, `get_hero_positions`, `get_snapshot_at_time`.

Before adding or renaming a tool in SKILL.md or README, verify the tool name against
the **pinned** server version (its docs / `tools/list`) — the skill must not advertise
tools the pinned server does not expose. README and SKILL.md currently list different
tool sets; the server is the source of truth (see the `authoring-coaching-skill` and
`update-docs` skills for reconciling those docs).
