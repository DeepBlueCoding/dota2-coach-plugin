---
name: dota2-coaching
description: |
  Professional Dota 2 coaching methodology for analyzing match replays.
  Use when: analyzing matches, reviewing deaths, evaluating farming patterns,
  discussing draft strategy, reviewing fights, or providing coaching feedback.
  Triggers on: "analyze match", "review deaths", "farming pattern", "draft analysis",
  "fight breakdown", "coaching", "what went wrong", "how to improve".
---

# Dota 2 Coaching Methodology

You have access to the Dota 2 Match Analysis MCP Server which provides raw game data from replays.
This skill teaches you HOW to interpret that data like a professional coach.

## Core Philosophy

- **Never dump raw data** - Every number must have context and meaning
- **Patterns over events** - Look for trends across multiple occurrences
- **Role-appropriate analysis** - Judge players by their position's responsibilities
- **Actionable advice** - Every insight should lead to something the player can DO differently

## Analysis Workflow

1. **Context First**: Always call `get_match_info` and `get_draft` before specifics
2. **Understand the Game State**: Use `get_timeline` to identify key momentum shifts
3. **Deep Dive**: Use specific tools for deaths, farming, fights based on what matters
4. **Synthesize**: Connect findings across different data sources

## Game Phases

### Laning Phase (0-10 minutes)
- Focus on CS comparisons, first blood, tower damage
- Deaths here are CRITICAL for carries, expected for offlaners
- Rune control and rotations define mid-game trajectory

### Mid Game (10-25 minutes)
- Objective trading, teamfight outcomes
- Item timing relative to expected benchmarks
- Map control and vision

### Late Game (25+ minutes)
- Buyback usage, high-ground attempts
- Carry positioning in fights
- Game-ending mistakes

## Position Responsibilities

See [ROLE_EXPECTATIONS.md](ROLE_EXPECTATIONS.md) for detailed role breakdowns.

**Quick Reference:**
- **Pos 1**: Farm, survive, carry late game. Deaths are catastrophic early.
- **Pos 2**: Farm, create tempo, rotate with runes. Playmaker role.
- **Pos 3**: Pressure enemy carry, initiate fights, absorb attention. Deaths acceptable.
- **Pos 4**: Roam, secure runes, enable kills. Primary early rotator.
- **Pos 5**: Protect carry, ward, stack. Sacrifice farm for team.

## Benchmarks

See [HERO_BENCHMARKS.md](HERO_BENCHMARKS.md) for detailed CS/GPM/XPM targets.

## Death Analysis Framework

See [DEATH_ANALYSIS.md](DEATH_ANALYSIS.md) for the complete framework.

**Quick Checklist:**
1. Was vision available?
2. Was it during a power spike window?
3. Did death trade for objective?
4. Buyback available/used correctly?
5. What item timing was delayed?

## Fight Analysis Framework

When reviewing teamfights:
1. **Initiation**: Who started? Was it planned or reactive?
2. **Target Priority**: Did the right heroes die first?
3. **Ability Usage**: Key ultimates used effectively?
4. **Positioning**: Did carries stay safe? Did supports get value?
5. **Trade Value**: What did each team gain/lose?

## Common Coaching Points

### For Carries (Pos 1)
- Farm efficiency: Are they hitting benchmarks?
- Death avoidance: Unnecessary deaths delay key items
- Fight participation: Joining too early vs too late
- Item choices: Adapting to enemy lineup

### For Mids (Pos 2)
- Lane control and rune securing
- Rotation timing (with good runes, not randomly)
- Tempo creation in mid-game
- Not overextending for kills

### For Offlaners (Pos 3)
- Creating space vs feeding
- Knowing when to leave lane
- Initiation timing in fights
- Building appropriate items (aura vs selfish)

### For Supports (Pos 4/5)
- Vision placement and dewarding
- Pull timing and execution
- Save usage on cores
- Sacrificing correctly (not dying for nothing)

## MCP Tools Reference

**Match Context:**
- `get_match_info` - Duration, winner, skill bracket
- `get_draft` - Picks, bans, lane assignments

**Death Analysis:**
- `get_hero_deaths` - All deaths with context
- `get_death_details` - Deep dive on specific death

**Farming Analysis:**
- `get_farming_pattern` - Minute-by-minute farming routes
- `get_cs_at_minute` - CS snapshots at specific times

**Fight Analysis:**
- `get_fights` - Detected teamfights with participants
- `get_fight_details` - Breakdown of specific fight

**Timeline:**
- `get_timeline` - Key events and networth swings
- `get_objective_kills` - Towers, Roshan, barracks

**Positioning:**
- `get_hero_positions` - Where heroes were at specific times
- `get_snapshot_at_time` - Full game state at a moment
