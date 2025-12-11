# Dota 2 Coach Plugin

A Claude Code plugin that combines professional Dota 2 replay analysis tools with expert coaching methodology.

## What This Plugin Provides

### MCP Server: Match Analysis Tools
- **Replay parsing**: Download and analyze Dota 2 replays
- **Death analysis**: Every death with context (killer, position, time)
- **Farming patterns**: Minute-by-minute CS, jungle routes, efficiency
- **Fight detection**: Teamfight identification with participants and outcomes
- **Timeline events**: Objectives, kills, networth swings
- **Pro scene data**: Player/team lookups, match history

### Skill: Coaching Methodology
- **Benchmarks**: CS/GPM/XPM targets by role and game phase
- **Death framework**: 5-question analysis for every death
- **Role expectations**: What each position should do and when
- **Pattern recognition**: How to identify trends, not just events

## Installation

```bash
/plugin install equilibrium-coach/dota2-coach-plugin
```

This installs both:
1. The MCP server (data extraction tools)
2. The coaching skill (analysis methodology)

## Usage Examples

### Analyze a Match
```
Analyze match 8247812539, focusing on the carry's performance
```

Claude will:
1. Load the coaching skill automatically
2. Call MCP tools to get match data
3. Apply benchmarks and frameworks to interpret the data
4. Provide actionable coaching advice

### Review Deaths
```
Review the deaths in match 8247812539 and tell me which were preventable
```

Claude will:
1. Use `get_hero_deaths` to fetch all deaths
2. Apply the DEATH_ANALYSIS.md framework
3. Categorize each death (unavoidable, preventable, acceptable)
4. Explain what could have been done differently

### Compare Farming
```
Compare the farming patterns of both carries in match 8247812539
```

Claude will:
1. Use `get_farming_pattern` for both carries
2. Reference HERO_BENCHMARKS.md for targets
3. Identify where each carry fell behind or excelled
4. Suggest improvements

## Plugin Structure

```
dota2-coach-plugin/
├── .claude-plugin/
│   └── plugin.json           # Plugin manifest
├── .mcp.json                  # MCP server configuration
├── skills/
│   └── dota2-coaching/
│       ├── SKILL.md           # Main coaching methodology
│       ├── HERO_BENCHMARKS.md # CS/GPM/XPM targets
│       ├── DEATH_ANALYSIS.md  # Death evaluation framework
│       └── ROLE_EXPECTATIONS.md # Position responsibilities
└── README.md
```

## MCP Tools Available

### Match Context
- `get_match_info` - Duration, winner, skill bracket
- `get_draft` - Picks, bans, lane assignments with positions

### Death Analysis
- `get_hero_deaths` - All deaths with killer, assists, position
- `get_death_details` - Deep dive on specific death

### Farming Analysis
- `get_farming_pattern` - Minute-by-minute farming routes
- `get_cs_at_minute` - CS snapshots at specific times

### Fight Analysis
- `get_fights` - Detected teamfights with participants
- `get_fight_details` - Breakdown of specific fight

### Timeline
- `get_timeline` - Key events and networth swings
- `get_objective_kills` - Towers, Roshan, barracks

### Pro Scene
- `search_pro_player` - Find players by name
- `get_pro_matches` - Recent professional matches
- `get_team_matches` - Team match history

## Requirements

- Claude Code CLI
- Internet connection (for replay downloads and OpenDota API)

## License

MIT

## Contributing

Issues and PRs welcome at [github.com/equilibrium-coach/dota2-coach-plugin](https://github.com/equilibrium-coach/dota2-coach-plugin)
