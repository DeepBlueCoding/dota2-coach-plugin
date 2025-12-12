# Death Analysis Framework

A systematic approach to evaluating deaths in Dota 2 replays.

## The 5-Question Framework

For EVERY death, ask these questions:

### 1. Was Vision Available?

**Check:**
- Was there a ward covering the area?
- Did the player check minimap before engaging/farming?
- Was smoke used by enemies? (Excusable if so)

**Coaching Points:**
- "You died in an unwarded area - was there a reason you didn't have vision there?"
- "The ward saw them coming 10 seconds before - why no reaction?"
- "Good death - smoked gank, hard to prevent"

### 2. Was It During a Power Spike Window?

**Vulnerable Windows:**
- Before level 6 (no ultimate)
- Before key item (BKB, Manta, Blink)
- When ultimate/key spell on cooldown

**Strong Windows:**
- Just hit level 6/12/18
- Just completed major item
- Full mana and abilities ready

**Coaching Points:**
- "You died 30 seconds before hitting level 6 - playing safer for 1 wave would've changed this"
- "Dying with Chrono on cooldown is unacceptable at this MMR"

### 3. Did the Death Trade for an Objective?

**Worth It:**
- Died but team took Roshan
- Died but team took 2+ towers
- Died but carry farmed freely (space creation)
- Died but enemy used critical cooldowns before upcoming fight

**Not Worth It:**
- Random death farming jungle
- Death defending already-lost tower
- Death chasing for kill that doesn't happen

**Coaching Points:**
- "This death got your team Roshan - acceptable trade"
- "You died for nothing - no objective, no space, just gold for enemy"

### 4. Was Buyback Available/Used Correctly?

**Buyback Considerations:**
- Was buyback available?
- If used, was there an immediate opportunity?
- If not used, did they lose a critical objective?

**When Buyback is Correct:**
- Defense of high ground
- Roshan fight where you can still contribute
- Team fight you can rejoin and win

**When Buyback is Wrong:**
- Random mid-game death with no immediate objective
- When enemy has already taken the objective
- When you'll just die again immediately

**Coaching Points:**
- "Good buyback - rejoined and won the fight"
- "You bought back to defend but they weren't even pushing - wasted gold"
- "Should have bought back here - lost rax because of it"

### 5. What Item Timing Was Delayed?

**Calculate the Impact:**
- Death costs: Net worth × respawn time in lost farm + gold lost to enemy
- Typical death = 1000-2000 gold setback
- Key item delays snowball

**Example Impact:**
- Death at 12:00 delays BKB from 18:00 to 20:00
- Those 2 minutes might cost 2 fights
- Those 2 fights cost 2 towers
- Snowball effect

---

## Death Categories

### Unavoidable Deaths (Don't Criticize)
- 5-man smoke gank with full execution
- Enemy snowballing from another lane
- Ping/connection issues (visible in replay as standing still)

### Preventable Deaths (Coach Hard)
- Bad positioning without escape
- Fighting without vision
- Greedy farming patterns
- TP'ing into obvious danger

### Acceptable Deaths (Context-Dependent)
- Creating space as pos 3
- Trading for objectives
- Forcing enemy to commit resources

### Throw Deaths (Critical Coaching Moments)
- Dying with Aegis
- Dying solo when team is winning
- Dying before scheduled Roshan
- Dying with buyback on cooldown in late game

---

## Death Pattern Analysis

### Solo Deaths
- More than 3 solo deaths usually indicates positioning issues
- Check if they were farming in dangerous areas
- Check if they were caught pushing waves alone

### Group Deaths (Team Wipes)
- Who initiated badly?
- Who was positioned wrong?
- Was it a forced fight or optional?

### Repeat Deaths
- Dying to same gank pattern = learning issue
- Dying in same area = vision/positioning issue
- Dying to same hero = itemization or awareness issue

---

## Questions to Ask Per Role

### Carry Deaths
1. "Was farming this area worth the risk?"
2. "Did you check enemy positions before committing?"
3. "Would a different item have saved you?"

### Mid Deaths
1. "Did you have rune vision?"
2. "Were enemy supports missing from other lanes?"
3. "Did you push the wave before leaving lane?"

### Offlane Deaths
1. "Did this death create space for your team?"
2. "Were you solo against how many heroes?"
3. "Could you have gotten more from this death?"

### Support Deaths
1. "Did you die saving a core?"
2. "Did you trade correctly (your death vs enemy resources)?"
3. "Was your positioning in the fight correct?"

---

## Using MCP Tools for Death Analysis

```
1. get_hero_deaths(match_id) - Get all deaths
2. For each important death:
   - get_hero_positions(match_id, minute) - Where were teammates?
   - get_snapshot_at_time(match_id, time) - Full game state
   - get_timeline(match_id) - Was objective happening?
3. Calculate death cost vs game impact
```
