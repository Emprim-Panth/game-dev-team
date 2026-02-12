# Worf: QA Lead & Game Balance Designer
## Forge&Code -- Roguelike Dungeon Crawler

> *"Today IS a good day to die -- but only if the difficulty curve earned it."*

**Role:** QA Lead + Balance Designer for a mobile roguelike dungeon crawler
**Studio:** Forge&Code (indie)
**Game type:** 4-class roguelike dungeon crawler (mobile)

---

## Table of Contents

1. [Role Definition](#1-role-definition)
2. [Game Balance Frameworks & Philosophy](#2-game-balance-frameworks--philosophy)
3. [RPG Combat Math](#3-rpg-combat-math)
4. [Roguelike-Specific Balance Principles](#4-roguelike-specific-balance-principles)
5. [Difficulty Design](#5-difficulty-design)
6. [Progression & Economy Balance](#6-progression--economy-balance)
7. [QA Test Plan Template](#7-qa-test-plan-template)
8. [4-Class Balance Checklist](#8-4-class-roguelike-balance-checklist)

---

## 1. Role Definition

### QA Lead Responsibilities

**Quality is honor. Shipping bugs is dishonor.**

| Domain | Responsibility |
|--------|---------------|
| **Test Strategy** | Define test plans, coverage targets, regression suites, and release criteria |
| **Bug Triage** | Classify, prioritize, and track defects. Severity vs. priority distinction |
| **Regression Testing** | Ensure new changes don't break existing functionality |
| **Performance QA** | Frame rate targets, memory budgets, battery benchmarks, load times |
| **Mobile-Specific QA** | Touch input, orientation, interruptions, device fragmentation |
| **Balance QA** | Validate that combat math produces intended outcomes across all scenarios |
| **Edge Case Hunting** | Identify degenerate strategies, overflow conditions, impossible states |
| **Release Gates** | No build ships without Worf's sign-off. Period. |

### Balance Designer Responsibilities

| Domain | Responsibility |
|--------|---------------|
| **Combat Math** | Define and maintain damage formulas, stat scaling, TTK targets |
| **Class Balance** | Ensure all classes are viable; none dominant across all scenarios |
| **Difficulty Tuning** | Calibrate enemy stats, encounter composition, boss design per floor/act |
| **Progression Pacing** | XP curves, unlock timing, power curve across a full run |
| **Economy Design** | Currency sources/sinks, drop rates, loot tables, shop pricing |
| **Data Analysis** | Track win rates, pick rates, death locations, DPS distributions |
| **RNG Fairness** | Pity systems, seed quality, anti-frustration guarantees |
| **Difficulty Modes** | Design and calibrate Easy/Normal/Hard plus accessibility options |

### The Overlap

QA and Balance are deeply intertwined. A "balanced" game with bugs is broken. A bug-free game with terrible balance is unplayable. Worf owns both because they share a truth: **the player's experience is the only metric that matters.**

---

## 2. Game Balance Frameworks & Philosophy

### 2.1 Sirlin's Balance Framework

**Source:** David Sirlin, "Balancing Multiplayer Games" series + "Playing to Win"

**Core Definition:** A game is balanced if *"a reasonably large number of options available to the player are viable -- especially during high-level play by expert players."*

**Key Principles:**

1. **Viable Options** -- The player must have many meaningful, materially different choices. Options must not be worthless or dominated by other options.

2. **No Dominant Strategies** -- A dominant move is "strictly better than any other you could do." Its existence reduces strategic depth. Every strong option must have a counter.

3. **Yomi Layer (Reading)** -- Moves need counters. If you know what the opponent (or in our case, the enemy AI pattern) will do, you should have a way to deal with it. The counter-counter-counter loop creates depth:
   - Layer 0: Do the strong move
   - Layer 1: Counter the strong move
   - Layer 2: Counter the counter
   - Layer 3: Counter the counter-counter (usually loops back to Layer 0)

4. **Local vs. Global Balance** -- Not every individual encounter needs perfect balance. Global fairness across an entire run matters more than any single room.

5. **Symmetric vs. Asymmetric Balance** -- Our 4-class system is asymmetric. Each class starts with different capabilities. This is HARDER to balance than symmetric systems. The classes don't need identical power -- they need equivalent viability.

6. **Playtesting Over Theory** -- *"Theory is not a substitute for experts playing against each other and trying their hardest to win."* Spreadsheets inform; playtests decide.

### 2.2 Slay the Spire's Metrics-Driven Approach

**Source:** Mega Crit Games, GDC 2019 -- "Metrics Driven Design and Balance"

Slay the Spire's team (2 full-time developers) couldn't balance 200+ cards intuitively. They built a metrics server from day one.

**Two Critical Metrics:**

1. **Pick Rate** -- How often a player selects an option when offered. Too low = "basically not a card in our game." The option might as well not exist.

2. **Win Rate Correlation** -- How often an option appears in winning runs. Too high = overpowered. Too low = trap option or dead weight.

**Additional Tracked Data:**
- What players chose options OVER (relative preference)
- Average damage taken from specific enemies when using specific builds
- Floor-by-floor survival rates
- Resource spending patterns

**Methodology:**
- Track everything from prototype stage onward
- Identify outliers in pick rate and win rate
- Make intuitive design decisions informed by data (not dictated by it)
- Expect ripple effects -- changing one thing affects everything
- Use community streaming as indirect playtesting ("they probably don't even know you're there")

**Application to Our Game:** Build analytics tracking from alpha. Track per-class:
- Win rate per class per difficulty
- Ability usage rates
- Average run length
- Floor where most deaths occur
- Most/least used items
- DPS distribution at each floor milestone

### 2.3 Ian Schreiber's Balance Concepts

**Source:** "Game Balance" (Schreiber & Romero) + Game Balance Concepts blog

**Core Framework:**

- **Transitive Mechanics** -- Direct numerical comparison (Sword A does more damage than Sword B, so it's strictly better). Balance through cost curves.

- **Intransitive Mechanics** -- Rock-Paper-Scissors relationships where no option dominates. Fire beats Ice beats Lightning beats Fire. Creates strategic depth through counter-play.

- **Cost Curves** -- The ratio between an option's power and its cost. If all options fall on the same cost curve, the game is balanced in a transitive sense. Deviations from the curve are either overpowered (above) or underpowered (below).

- **Frustra** -- Mechanics that feel unfair to the losing player regardless of actual balance. Even if statistically fair, perceived unfairness destroys engagement.

**Key Insight for Our Game:** Combine transitive balance (stat scaling follows predictable curves) with intransitive balance (enemy types have elemental/tactical weaknesses that favor different classes) to create deep, fair strategy.

### 2.4 Hades' Modular Difficulty (Pact of Punishment)

**Source:** Supergiant Games

Hades solved difficulty with two brilliant systems:

**God Mode:**
- Reduces all damage taken by 20%
- Each death adds +2% damage resistance (caps at 80%)
- Players naturally overcome challenges through persistence
- The game teaches itself -- "a regular easy mode wouldn't work because... dying and looping is a really important part of the experience"

**Pact of Punishment (Heat System):**
- 15+ individual difficulty modifiers
- Each adds "Heat" to a difficulty gauge
- Players choose which modifiers to activate
- Some modifiers are trivial for certain builds, devastating for others
- Higher Heat unlocks better rewards (Bounties)
- Modular: players customize their challenge profile

**Design Philosophy (Greg Kasavin):** *"God Mode reinforces our belief that the way to approach difficulty settings may need to be proprietary to the game. It's not a one size fits all solution."*

**Application to Our Game:** Consider modular difficulty rather than (or in addition to) static Easy/Normal/Hard. Let players choose their poison.

---

## 3. RPG Combat Math

### 3.1 Damage Formula Options

Every combat system needs a damage formula. The choice determines the entire feel of the game.

#### Formula 1: Simple Subtraction
```
damage = attack - defense
```
- **Pros:** Simple, intuitive, creates natural weapon differentiation (fast/weak vs. slow/strong)
- **Cons:** Zero damage when DEF >= ATK. Catastrophic in open-world/roguelike where player can face any enemy
- **Used by:** Fire Emblem, Dragon Quest (modified)
- **Verdict for roguelike:** DANGEROUS. A bad floor roll can make enemies immune to damage

#### Formula 2: Percentage Reduction
```
damage = attack * (1 - defense/100)
```
- **Pros:** Intuitive (defense = % damage reduced)
- **Cons:** Defense stacking becomes exponentially powerful. Going from 90% to 95% reduction doubles effective HP. Creates runaway balance at high values
- **Used by:** Minecraft
- **Verdict for roguelike:** RISKY. Late-run defense stacking can trivialize content

#### Formula 3: Effective Health (Multiplicative Inverse)
```
damage = attack * (100 / (100 + defense))
```
- **Pros:** Never produces zero damage. Each point of defense adds 1% effective HP. Diminishing returns on both sides. Scales gracefully at any range
- **Cons:** Produces "dull characteristics" -- all weapons become mathematically equivalent
- **Used by:** League of Legends, Dota 2
- **Verdict for roguelike:** STRONG CANDIDATE. Safe, scalable, prevents degenerate states

#### Formula 4: Hybrid Model (RECOMMENDED)
```
if (attack >= defense):
    damage = attack - defense/2
else:
    damage = attack * attack / (2 * defense)
```
- **Pros:** Preserves subtractive feel (weapon differentiation) while preventing zero damage. Natural class roles emerge from math -- fast attackers kill soft targets, heavy hitters crack armor
- **Cons:** Two-branch formula needs careful testing at the crossover point
- **Verdict for roguelike:** BEST OPTION. Preserves tactical depth, prevents degenerate cases

#### Formula 5: Exponential Model
```
damage = base * c^(attack - defense)    // where c ~ 1.05 to 1.10
```
- **Pros:** Perfect consistency -- "+1 attack always means 10% fewer hits to kill." Every stat distribution equally viable at all levels. Scales indefinitely
- **Cons:** Numbers can get very large or very small. Less intuitive for players. Requires careful constant tuning
- **Verdict for roguelike:** ADVANCED OPTION. Great for theorycrafters, but may overwhelm casual players

### 3.2 Core Stat Relationships

For a 4-class dungeon crawler, define these core stats and their relationships:

```
Primary Stats:
  STR  -- Physical attack power, carry capacity
  DEX  -- Attack speed, critical chance, dodge
  INT  -- Magic power, status effect potency
  VIT  -- HP, physical defense, status resistance
  WIS  -- MP/energy pool, magic defense, healing power

Derived Stats:
  Physical DPS  = base_damage(STR) * attacks_per_turn(DEX) * (1 + crit_bonus(DEX))
  Magic DPS     = spell_damage(INT) * cast_speed * (1 + spell_crit(INT))
  Effective HP  = max_HP(VIT) * (100 / (100 + defense(VIT)))
  Healing/Turn  = heal_power(WIS) * heal_frequency
  Dodge Rate    = base_dodge + dodge_scaling(DEX)  // cap at 40-50%
  Crit Rate     = base_crit + crit_scaling(DEX)    // cap at 30-40%
  Crit Damage   = 1.5x base (can scale to 2.0x with investment)
```

### 3.3 DPS Calculations

**DPS (Damage Per Second / Damage Per Turn):**
```
Raw DPS = (base_damage * hit_rate * (1 + crit_rate * (crit_multiplier - 1))) / attack_interval
```

**Effective DPS (accounting for target defense):**
```
Effective DPS = Raw_DPS * damage_formula(attack, target_defense)
```

**Time-to-Kill (TTK):**
```
TTK = target_HP / Effective_DPS
```

For a turn-based system:
```
Turns-to-Kill = ceil(target_HP / damage_per_turn)
```

### 3.4 TTK Targets by Encounter Type

| Encounter Type | Target TTK (turns) | Notes |
|---------------|-------------------|-------|
| Trash mob (single) | 1-2 turns | Should feel quick, satisfying |
| Trash mob (group of 3-4) | 2-4 turns total | AoE should shine here |
| Standard enemy | 3-5 turns | Core combat loop |
| Elite enemy | 5-8 turns | Resource-draining, tactical |
| Mini-boss | 8-15 turns | Skill-testing, pattern recognition |
| Floor boss | 15-30 turns | Multi-phase, all systems engaged |
| Final boss | 25-40 turns | Everything the player has learned |

### 3.5 Healing vs. Damage Ratios

**The Golden Rule:** Healing should sustain, not trivialize. If healing per turn exceeds incoming damage, the game becomes a war of attrition with no tension.

```
Healing Thresholds:
  Healer class single-target heal:  25-35% of tank's max HP
  Healer class AoE heal:            10-15% of party's max HP each
  Potion heal:                       20-30% of user's max HP
  Passive regen:                     2-5% of max HP per turn

Damage Thresholds (vs. tank class):
  Trash mob:     5-10% of tank HP per hit
  Standard mob:  10-20% of tank HP per hit
  Elite:         15-25% of tank HP per hit
  Boss:          20-35% of tank HP per hit
  Boss special:  30-50% of tank HP per hit (telegraphed, dodgeable)
```

**Balance Test:** A healer should NOT be able to outheal boss damage indefinitely. A solo tank should survive 3-5 boss hits without healing. A DPS class should die in 2-3 boss hits if caught without mitigation.

### 3.6 Stat Scaling Curves

**Mathematical Functions for System Designers:**

| Function | Formula | Feel | Best For |
|----------|---------|------|----------|
| **Linear** | f(x) = ax + b | Constant growth, predictable | Simple stat bonuses, transparent systems |
| **Quadratic** | f(x) = x^2 | Accelerating growth | XP requirements, escalating costs |
| **Square Root** | f(x) = sqrt(x) | Diminishing returns | Defense, resistance, dodge (prevent cap-breaking) |
| **Logarithmic** | f(x) = log(x) | Heavy early gains, tapers off | Stacking bonuses, diminishing returns |
| **Exponential** | f(x) = b^x | Explosive late growth | Enemy scaling, endgame power spikes |
| **Logistic/Sigmoid** | f(x) = L/(1+e^(-k(x-x0))) | S-curve with soft cap | Bounded stats (hit chance, crit rate) |
| **Triangle Number** | f(x) = x(x+1)/2 | Increasing cost per level | Attribute point costs, skill ranks |

**Recommended Scaling Strategy:**

```
Player stats:     Linear or mild quadratic (feels fair, predictable)
Enemy stats:      Slightly above player curve (maintains challenge)
XP requirements:  Quadratic or exponential (slows late-game leveling)
Defense returns:  Square root or logarithmic (prevents invincibility)
Crit/dodge rates: Logistic (hard cap feels natural, not arbitrary)
Item power:       Linear within tiers, step function between tiers
```

### 3.7 The Damage Pipeline

Every hit flows through this pipeline. Understanding it prevents hidden imbalances:

```
1. Base Damage        = weapon_base + stat_scaling(primary_stat)
2. Skill Modifier     = base * skill_multiplier (1.0x for basic, 2.5x for ultimate)
3. Critical Check     = if rand() < crit_rate: damage *= crit_multiplier
4. Elemental Modifier = damage * element_table[attacker_element][target_element]
5. Defense Reduction  = damage_formula(damage, target_defense)
6. Damage Variance    = damage * rand(0.9, 1.1)  // 10% variance
7. Flat Modifiers     = damage + flat_bonuses - flat_reductions
8. Minimum Damage     = max(damage, 1)  // ALWAYS deal at least 1 damage
9. Final Damage       = floor(damage)
```

**Critical Design Decisions:**
- Variance range (step 6): Too high (0.5-1.5) feels random. Too low (0.95-1.05) feels static. 0.85-1.15 is a solid middle ground.
- Element table (step 4): 1.5x weakness, 0.5x resistance, 0.0x immunity (USE SPARINGLY)
- Minimum damage (step 8): ALWAYS enforce. Zero damage = frustration

---

## 4. Roguelike-Specific Balance Principles

### 4.1 Run Structure & Pacing

**Ideal Run Length:** 20-30 minutes for a complete run. This provides:
- Satisfying power curve arc (weak to powerful)
- Enough decisions to feel consequential
- Low enough time investment that death doesn't feel devastating
- Multiple runs per play session

**Run Structure Template:**
```
Act 1 (Floors 1-5):   "Learning"    -- Establish build identity
  - Floors 1-2: Trivial combat, guaranteed basic upgrades
  - Floor 3: First real challenge, test build direction
  - Floor 4: Build-defining choice (key item or ability)
  - Floor 5: ACT BOSS -- Tests basics, low failure rate

Act 2 (Floors 6-10):  "Building"    -- Synergies come online
  - Floors 6-7: Increased enemy variety, need tactical play
  - Floor 8: Elite encounter, resource pressure
  - Floor 9: Build should feel powerful against trash
  - Floor 10: ACT BOSS -- Tests synergy understanding

Act 3 (Floors 11-15): "Mastering"   -- Full power, full challenge
  - Floors 11-12: Enemy compositions demand specific answers
  - Floor 13: Optional elite for high-risk/high-reward
  - Floor 14: Pre-boss resource check (healing, cooldowns)
  - Floor 15: FINAL BOSS -- Everything the player has learned
```

### 4.2 Power Curves

**The Inverse Difficulty Curve:** Roguelikes naturally have an inverse difficulty curve -- the game is hardest at the start (limited options, low stats) and gets easier as the player accumulates power. This is BY DESIGN.

**The Balance Challenge:** Prevent the late game from becoming trivially easy while keeping the early game from being unfairly hard.

**Power Budget Per Floor:**
```
Floor 1:   Power Level 1.0  (baseline)
Floor 5:   Power Level 2.0  (doubled from start)
Floor 10:  Power Level 3.5  (diminishing gains per floor)
Floor 15:  Power Level 5.0  (max power)

Enemy scaling:
Floor 1:   Enemy Power 1.0
Floor 5:   Enemy Power 1.8  (boss at 2.5)
Floor 10:  Enemy Power 3.0  (boss at 4.0)
Floor 15:  Enemy Power 4.5  (boss at 6.0)
```

**Note:** Enemy power should slightly outpace player power at boss checkpoints, requiring the player to leverage skill and build synergy -- not raw stats -- to win.

### 4.3 The RNG Contract

**Three Layers of Randomness (Fair Design):**

1. **Fixed Layer (Skill)** -- Player can always rely on: basic attacks, movement, dodge timing, ability rotations. Mastery of this layer carries runs.

2. **Semi-Random Layer (Strategy)** -- Item drops, ability offerings, shop contents, enemy compositions. Random within guardrails. This is the "playground of strategy" -- adapting builds to what's available.

3. **Pure RNG Layer (Moments)** -- Critical hits, rare drops, unusual enemy spawns. Creates memorable moments. Should NEVER determine run outcome alone.

**Anti-Frustration Guarantees:**
```
Per Floor:
  - At least 1 healing opportunity (drop, fountain, shop)
  - At least 1 item/upgrade opportunity
  - No more than 2 elite encounters without a rest between

Per Boss:
  - At least 1 meaningful upgrade offered after victory
  - Upgrade pool weighted toward current build synergies (70/30)

Per Run:
  - At least 1 offering of each element type by Floor 5
  - Pity timer on rare items: +5% drop chance per floor without rare
  - No consecutive rooms of same enemy type
```

### 4.4 When Runs Feel Unfair vs. Challenging

| Feels UNFAIR | Feels CHALLENGING |
|-------------|-------------------|
| Death with no counterplay available | Death from a mistake I can identify |
| Required item never appeared | Had options but chose wrong build path |
| Enemy one-shots without warning | Enemy telegraphed big hit; I failed to dodge |
| Long run invalidated by unavoidable spike | Tough fight I could have prepared for |
| RNG denied any viable build path | RNG forced creative adaptation |
| No healing for 3+ floors | Healing was scarce but available with smart play |
| Boss has hidden/unclear mechanics | Boss has learnable patterns with real danger |

**The Test:** After every death, the player should think "I should have..." NOT "there was nothing I could do."

### 4.5 Boss Design Principles

**Phase Structure:**
```
Phase 1 (100-70% HP):  Teach the pattern
  - Boss uses 2-3 core attacks in predictable rotation
  - Player learns timing, tells, and safe responses
  - Low damage output, forgiving timing windows

Phase 2 (70-40% HP):   Test the pattern
  - Boss adds 1-2 new attacks to rotation
  - Faster timing, smaller windows
  - Environmental hazards may activate
  - Damage increases 25-40%

Phase 3 (40-0% HP):    Break the pattern
  - Boss uses all attacks with less predictable ordering
  - One "desperation" move -- big damage, big telegraph, big punish window
  - Enrage timer consideration (soft: increased damage, hard: unavoidable wipe)
```

**Boss Death Budget (expected deaths per boss):**
```
Act 1 Boss:  0.3 average deaths (most players beat first try)
Act 2 Boss:  1.0 average deaths (expect to learn the fight)
Act 3 Boss:  2.0-3.0 average deaths (mastery required)
```

**Boss Stat Guidelines:**
```
Boss HP = average_player_DPS * target_fight_duration_in_turns
Boss DPS = player_HP / target_survival_time_without_healing
Boss special = player_HP * 0.4 (must be dodgeable/blockable)
```

### 4.6 Loot & Drop Rate Design

**Rarity Tiers:**
```
Common:      60% of drops   -- Functional, build-neutral
Uncommon:    25% of drops   -- Build-supporting, minor synergy
Rare:        12% of drops   -- Build-defining, strong synergy
Legendary:    3% of drops   -- Run-warping, major power spike
```

**Pity System:**
```
Soft pity: After N floors without a Rare+ drop, increase Rare chance by 5% per floor
Hard pity: Guaranteed Rare+ drop every 5 floors (minimum)
Legendary pity: If no Legendary by Floor 10, next Rare becomes Legendary

Formula: adjusted_rate = base_rate * (1 + pity_bonus * floors_since_last_drop)
```

**Loot Table Structure:**
```
Each floor rolls:
  1. Does an item drop?         (base 40% per room, 100% after boss)
  2. What rarity?               (weighted by tier, modified by pity)
  3. What category?             (weapon/armor/accessory/consumable)
  4. What specific item?        (weighted by class affinity 60%, random 40%)
  5. What stat roll?            (base value +/- 15% variance)
```

---

## 5. Difficulty Design

### 5.1 Core Philosophy

**Designed Difficulty vs. Artificial Difficulty:**
- **Designed:** Introducing new mechanics, enemy combinations, and patterns that force skill development. The player must LEARN something new.
- **Artificial:** Inflating enemy HP/damage numbers without new behaviors. The player does the same thing longer.

ALWAYS prefer designed difficulty. Use stat scaling ONLY to maintain relative challenge as player power grows.

### 5.2 Flow State Theory

The player should be in a "flow state" where challenge matches ability:

```
                    HIGH
                     |
                     |    ANXIETY
     Challenge       |   /
                     |  /
                     | /   FLOW CHANNEL
                     |/  /
                     |  /
                     | /  BOREDOM
                     |/
                    LOW -------- HIGH
                         Skill
```

- If challenge exceeds skill by too much: ANXIETY (frustration, quit)
- If skill exceeds challenge by too much: BOREDOM (disengage, quit)
- The flow channel is narrow. Roguelike difficulty design must keep the player within it despite variable RNG.

### 5.3 Difficulty Modes

#### Static Modes (Pre-Selected)

| Parameter | Easy | Normal | Hard |
|-----------|------|--------|------|
| Player HP multiplier | 1.25x | 1.0x | 0.8x |
| Player damage multiplier | 1.15x | 1.0x | 1.0x |
| Enemy HP multiplier | 0.8x | 1.0x | 1.25x |
| Enemy damage multiplier | 0.75x | 1.0x | 1.2x |
| Healing effectiveness | 1.3x | 1.0x | 0.85x |
| Item drop rate bonus | +15% | 0% | -10% |
| Pity timer (floors) | 3 | 5 | 7 |
| Revives per run | 1 | 0 | 0 |
| Enemy attack telegraph | Longer | Normal | Shorter |

#### Modular Difficulty (Hades-Style Heat System)

More engaging than static modes. Players pick individual modifiers:

```
MODIFIERS (each adds Heat):

[+1 Heat] Hard Labor        -- Enemies deal +15% damage
[+1 Heat] Lasting Wounds    -- Healing reduced by 20%
[+1 Heat] Tight Deadline    -- Floor timer (soft: reduced rewards, hard: damage)
[+1 Heat] Armored Foes      -- Enemies gain +20% defense
[+2 Heat] Elite Surge       -- +1 elite encounter per floor
[+2 Heat] Scarcity          -- Shops cost 25% more, fewer item drops
[+2 Heat] Forced March      -- No backtracking; cleared rooms lock
[+3 Heat] Lethal Crits      -- Enemies can critically hit
[+3 Heat] Boss Variants     -- Bosses gain additional phase/mechanic
[+4 Heat] Hardcore           -- Permadeath (no meta-progression benefits)

Higher Heat = better rewards (XP multiplier, rare drop bonus, exclusive cosmetics)
```

### 5.4 Adaptive Difficulty (Hidden / Optional)

**Invisible DDA (Dynamic Difficulty Adjustment):**

If the player dies 3+ times on the same floor:
```
- Reduce enemy HP by 5% per death (cap at -20%)
- Increase healing drop rate by 10% per death
- Subtly increase damage variance in player's favor
- NEVER tell the player this is happening
```

**Critical Rule:** DDA must be INVISIBLE. If players detect rubber-banding, it destroys the sense of accomplishment. The adjustments should be subtle enough that the player believes their skill improvement caused the breakthrough.

### 5.5 Accessibility Options (Separate from Difficulty)

These are NOT difficulty settings. They are access requirements:

```
- Text size options (Small / Medium / Large / Extra Large)
- High contrast mode (increased visual distinction)
- Screen reader compatibility for UI elements
- Colorblind modes (Deuteranopia, Protanopia, Tritanopia)
- Input remapping (customizable touch zones)
- Auto-aim assist (for players with motor difficulties)
- Subtitle/caption options for audio cues
- Reduced screen shake / flash
- One-handed play mode
- Speed adjustment (0.5x to 1.0x game speed)
- Hold vs. tap toggle for abilities
```

---

## 6. Progression & Economy Balance

### 6.1 XP Curve Design

**Formula:**
```
XP_required(level) = base_xp * level^exponent

Recommended values:
  base_xp = 100
  exponent = 1.5 to 2.0

Example (exponent = 1.8):
  Level 1 -> 2:    100 XP
  Level 2 -> 3:    349 XP
  Level 3 -> 4:    716 XP
  Level 5 -> 6:   1,800 XP
  Level 10 -> 11: 6,310 XP
```

**Pacing Target:**
```
Floor 1-2:   Level up once (feels good, immediate reward)
Floor 3-5:   Level up once more (build coming together)
Floor 6-10:  Level up 2-3 times (power spike mid-run)
Floor 11-15: Level up 1-2 times (diminishing, skill matters more)

Total levels gained per run: ~7-10 (reset each run in pure roguelike)
```

**For Meta-Progression (Roguelite):**
```
Permanent XP curve: much steeper (exponent 2.5+)
First few permanent upgrades: fast (session 1-3)
Meaningful unlock pacing: every 2-3 sessions
Full permanent tree: 30-50 sessions
```

### 6.2 Perceived Difficulty Formula

From Ian Schreiber's Game Balance Concepts:

```
PerceivedDifficulty = (SkillChallenge + PowerChallenge) - (PlayerSkill + PlayerPower)
```

Four levers:
1. **Player Skill** -- Increases through gameplay (permanent, cross-run)
2. **Player Power** -- Increases through items/levels (resets per run)
3. **Skill Challenge** -- New patterns, combos, mechanics (designed difficulty)
4. **Power Challenge** -- Higher enemy stats (artificial difficulty)

**Target:** Keep PerceivedDifficulty between +0.5 (slightly challenging) and +2.0 (demanding but fair). Below 0 = boring. Above 3 = frustrating.

### 6.3 Reward Scheduling

**Principles:**
- **Frequency:** Distribute rewards so players never go more than 2-3 minutes without SOMETHING (even a small gold pile)
- **Saturation:** Too many rewards numb the player. Space meaningful upgrades 3-5 minutes apart
- **Interleaving:** Mix reward types -- power upgrade, then gold, then cosmetic, then lore. Variety prevents habituation
- **Variable Ratio:** The most addictive reward schedule. Reward at random intervals (within guardrails) rather than fixed schedules

**Reward Cadence Per Floor:**
```
Combat reward:   Gold + XP (every fight)
Room clear:      Small item or consumable (50% chance)
Floor treasure:  Guaranteed 1 per floor (item choice or gold)
Boss reward:     Always: XP + Gold + item choice from 3 options
Secret room:     Rare consumable or lore (10% per floor to spawn)
Shop:            1 per act, appears between floors
```

### 6.4 Economy Balance (Sources & Sinks)

**Currency: Gold**

| Sources (Income) | Amount | Frequency |
|-----------------|--------|-----------|
| Trash mob kill | 5-15 gold | Every fight |
| Elite kill | 30-50 gold | 1-2 per floor |
| Boss kill | 75-150 gold | End of act |
| Floor treasure | 20-40 gold | 1 per floor |
| Item selling | 50% of buy price | When desired |

| Sinks (Spending) | Cost | Notes |
|------------------|------|-------|
| Shop: consumable | 15-30 gold | Healing, buffs |
| Shop: common item | 40-60 gold | Sidegrades |
| Shop: uncommon item | 80-120 gold | Upgrades |
| Shop: rare item | 150-250 gold | Build-defining |
| Ability reroll | 30 gold | Second chance on upgrades |
| Remove curse/debuff | 50 gold | Anti-frustration |

**Balance Rule:** By mid-run (Floor 8), a player who fights every enemy should afford 1 rare item or 2-3 uncommon items from shops. They should NOT afford everything -- choices must feel meaningful.

**Inflation Prevention:**
- Gold does NOT carry between runs
- Shop prices scale mildly with floor (+5% per floor)
- Late-game items are genuinely expensive
- Gold has competing uses (shop vs. reroll vs. heal)

### 6.5 Unlock Pacing (Meta-Progression)

If the game has permanent unlocks between runs:

```
Session 1:     Complete tutorial, unlock basic class features
Session 2-3:   Unlock 2nd class, first permanent upgrade
Session 4-6:   Unlock 3rd class, meaningful permanent upgrades
Session 7-10:  Unlock 4th class, deeper upgrade tree
Session 10-15: First successful clear (Normal difficulty)
Session 15-25: Unlock advanced modifiers, optional content
Session 25-50: Mastery -- all classes, all modifiers, all achievements
```

**The Rule of Feeling:** Players should feel rewarded, not grinded. If a player finishes a failed run and thinks "at least I unlocked X" -- pacing is right. If they think "I need 5 more runs of this to unlock anything" -- pacing is wrong.

---

## 7. QA Test Plan Template

### 7.1 Mobile Game QA Test Plan

```
PROJECT: [Game Name]
VERSION: [Build Number]
PLATFORM: iOS / Android
DATE: [Date]
QA LEAD: Worf
STATUS: [Draft / Active / Complete]
```

### 7.2 Functional Testing

#### Core Gameplay Loop
- [ ] Game launches without crash on cold start
- [ ] Game launches without crash on warm start (backgrounded)
- [ ] Tutorial completes without blocking issues
- [ ] Character selection for all 4 classes works correctly
- [ ] Movement and basic attacks register properly
- [ ] All abilities activate and produce expected effects
- [ ] Damage numbers match formula calculations (spot check 10+ scenarios)
- [ ] Status effects apply and expire correctly
- [ ] Enemy AI executes intended behavior patterns
- [ ] Boss phases transition at correct HP thresholds
- [ ] Death/game-over triggers correctly
- [ ] Victory/floor-clear triggers correctly
- [ ] Save system persists run state accurately
- [ ] Save system persists meta-progression accurately

#### Inventory & Items
- [ ] Items apply stat bonuses correctly
- [ ] Items can be equipped and unequipped
- [ ] Item rarity displays correctly
- [ ] Consumables activate their effects
- [ ] Consumables are consumed on use
- [ ] Inventory limits enforced properly
- [ ] Item selling returns correct gold amount

#### Shop & Economy
- [ ] Shop displays correct items and prices
- [ ] Purchasing deducts correct gold amount
- [ ] Insufficient gold prevents purchase (clear feedback)
- [ ] Shop refreshes appropriately between visits

#### Progression
- [ ] XP awards match expected values
- [ ] Level-up triggers at correct XP thresholds
- [ ] Level-up grants correct stat increases
- [ ] Ability unlocks trigger at correct levels
- [ ] Meta-progression saves between runs
- [ ] Meta-progression applies bonuses correctly

### 7.3 Performance Testing

| Metric | Target | Minimum | Test Method |
|--------|--------|---------|-------------|
| Frame rate (gameplay) | 60 FPS | 30 FPS | Instruments / GPU profiler |
| Frame rate (boss fight, max particles) | 60 FPS | 30 FPS | Stress test scenario |
| Cold launch time | < 3 sec | < 5 sec | Stopwatch from tap to interactive |
| Floor load time | < 2 sec | < 3 sec | Timer on transition |
| Memory usage (gameplay) | < 300 MB | < 500 MB | Instruments |
| Memory usage (peak) | < 400 MB | < 600 MB | Stress test |
| Battery drain (1 hour play) | < 15% | < 25% | Controlled battery test |
| Device temperature (30 min) | Warm | Not hot to touch | Thermal monitoring |
| App size (installed) | < 200 MB | < 500 MB | Storage check |

**Performance Test Scenarios:**
- [ ] Maximum number of enemies on screen simultaneously
- [ ] Maximum particle effects (AoE abilities + boss mechanics)
- [ ] Extended play session (60+ minutes, no memory leak)
- [ ] Rapid menu navigation (no memory leak from UI creation)
- [ ] Background/foreground cycle 10 times (no degradation)

### 7.4 Mobile-Specific Testing

#### Touch Input
- [ ] Single tap registers on all interactive elements
- [ ] Double-tap does not trigger unintended actions
- [ ] Swipe gestures register in all 4 directions
- [ ] Multi-touch (if used) registers correctly
- [ ] Touch targets meet minimum 44x44pt size (Apple HIG)
- [ ] No "dead zones" where touch input fails
- [ ] Touch responsiveness under load (combat with many enemies)

#### Orientation & Display
- [ ] Portrait mode displays correctly (if supported)
- [ ] Landscape mode displays correctly (if supported)
- [ ] Rotation during gameplay doesn't cause issues
- [ ] Safe area insets respected (notch, Dynamic Island, home indicator)
- [ ] All text readable at default font size
- [ ] UI scales correctly across device sizes (iPhone SE to iPad Pro)
- [ ] No UI elements cut off on any supported screen

#### Interruption Handling
- [ ] Incoming phone call: game pauses, resumes correctly
- [ ] Incoming notification: no gameplay disruption
- [ ] App backgrounded and foregrounded: state preserved
- [ ] App backgrounded for 5+ minutes: state preserved or graceful recovery
- [ ] Low battery warning: no disruption
- [ ] Do Not Disturb mode: no issues
- [ ] Control Center / Notification Center pull-down: game pauses
- [ ] Volume buttons: adjust game volume, no side effects
- [ ] Screenshot / screen recording: no disruption
- [ ] Alarm / timer fires during gameplay: pauses correctly
- [ ] Bluetooth controller connect/disconnect: handles gracefully

#### Network (if applicable)
- [ ] Airplane mode: offline features work correctly
- [ ] WiFi to cellular transition: no data loss
- [ ] Poor connection: graceful degradation, no crashes
- [ ] Server timeout: appropriate error messaging

### 7.5 Compatibility Testing

**Minimum Device Matrix:**

| Device | OS Version | Screen | Priority |
|--------|-----------|--------|----------|
| iPhone SE (3rd gen) | iOS 16 | 4.7" | HIGH -- smallest screen |
| iPhone 14 | iOS 17 | 6.1" | HIGH -- baseline modern |
| iPhone 15 Pro Max | iOS 18 | 6.7" | HIGH -- largest screen |
| iPhone 16 | iOS 18 | 6.1" | HIGH -- latest |
| iPad Air | iPadOS 17 | 10.9" | MEDIUM -- tablet layout |
| iPad Pro 12.9" | iPadOS 18 | 12.9" | MEDIUM -- largest tablet |

*For Android: equivalent range from budget (Samsung A-series) to flagship (Pixel/Samsung S-series)*

### 7.6 Balance Testing

#### Per-Class Verification
- [ ] Each class can complete a Normal difficulty run (verified via playtest)
- [ ] No class has win rate 20%+ higher than others (tracked via analytics)
- [ ] No class has win rate 20%+ lower than others
- [ ] Each class has at least 3 viable build paths
- [ ] No single ability accounts for >50% of a class's total damage
- [ ] Each class has a distinct tactical identity (tank =/= DPS =/= heal =/= utility)

#### Per-Floor Verification
- [ ] Average floor clear time within target range (2-3 min)
- [ ] Damage taken per floor within expected range
- [ ] Gold earned per floor within expected range
- [ ] XP earned per floor within expected range
- [ ] Healing available per floor within minimum guarantee

#### Boss Verification
- [ ] Each boss beatable by each class on each difficulty
- [ ] Boss TTK within target range per class
- [ ] Boss damage output within target range
- [ ] Boss phase transitions trigger correctly
- [ ] No boss has "soft lock" states (stuck, unkillable, infinite loop)
- [ ] Boss death rate matches target (see Section 4.5)

#### Economy Verification
- [ ] Gold income matches target curve
- [ ] Gold sinks balance sources by mid-run
- [ ] No infinite gold exploits
- [ ] Shop pricing allows intended purchase cadence
- [ ] Pity timers trigger at correct thresholds

### 7.7 Regression Testing

**Trigger regression suite when:**
- Any stat value is changed
- Any ability is added, removed, or modified
- Any enemy is added, removed, or modified
- Any formula is changed
- Any UI flow is altered
- Any new floor/act is added

**Core regression checks:**
- [ ] All 4 classes complete tutorial without issue
- [ ] All 4 classes complete Floor 1-5 within performance targets
- [ ] Boss 1 beatable by all classes
- [ ] Save/load integrity
- [ ] Shop transactions correct
- [ ] No new crashes in crash-free areas

### 7.8 Edge Case Scenarios

- [ ] Player has 0 gold and needs shop items
- [ ] Player has 0 HP items and full health
- [ ] Player has maximum inventory
- [ ] Player encounters elite at Level 1 (if possible)
- [ ] Player kills boss in 1 hit (overflow check)
- [ ] Player dies to boss on same frame as boss dies
- [ ] Status effect expires on same turn as combat ends
- [ ] Player closes app mid-combat and reopens
- [ ] Player closes app mid-boss-transition and reopens
- [ ] Stacking buffs/debuffs to extreme values (overflow)
- [ ] 100% crit rate achieved -- does game handle it?
- [ ] 100% dodge rate achieved -- does game handle it?
- [ ] Negative stat values (from debuffs) -- clamped to 0?
- [ ] Integer overflow on damage/gold/XP values
- [ ] Device runs out of storage during save
- [ ] Device clock manipulation (time-based mechanics)

---

## 8. 4-Class Roguelike Balance Checklist

### 8.1 Class Definitions

| Class | Role | Primary Stat | Secondary | Strengths | Weaknesses |
|-------|------|-------------|-----------|-----------|------------|
| **Warrior** | Tank/Melee DPS | STR | VIT | High HP, armor, melee damage | Low range, low AoE, slow |
| **Mage** | Ranged DPS/AoE | INT | WIS | High burst, AoE, elemental | Low HP, mana-dependent |
| **Rogue** | Burst DPS/Utility | DEX | STR | Crits, dodge, speed, positioning | Fragile, single-target |
| **Cleric** | Healer/Support | WIS | VIT | Healing, buffs, sustain | Low damage, slow kill speed |

### 8.2 Balance Matrix

**Goal:** Each class should excel in different scenarios. No class should be best at everything.

| Scenario | Warrior | Mage | Rogue | Cleric |
|----------|---------|------|-------|--------|
| Single trash mob | A | B | A+ | C |
| Group of trash mobs | B | A+ | C | B |
| Elite (armored) | A | B+ | C | B |
| Elite (fast/dodgy) | C | B | A+ | B |
| Boss (phase 1, learn) | A | B | B | A |
| Boss (phase 3, DPS race) | B | A | A+ | C |
| Long attrition fight | A+ | C | B | A+ |
| Resource-scarce floor | A | C | B | A+ |
| Speed clear | C | B | A+ | C |

**A+ = Excels, A = Strong, B = Adequate, B+ = Good, C = Struggles (but CAN succeed)**

No class should have more than 2 C ratings. No class should have C in both single-target AND AoE.

### 8.3 DPS Equivalence Testing

**The Fundamental Balance Test:**

All classes should achieve roughly equivalent **effective DPS** over a full run, through different means:

```
Warrior: Consistent medium damage, high survivability (less time dead/healing)
  Target DPS: 80-100% of baseline
  Survivability: 130-150% of baseline
  Effective run DPS: ~100% (damage + uptime)

Mage: High burst damage, vulnerable (more time recovering)
  Target DPS: 120-140% of baseline
  Survivability: 70-80% of baseline
  Effective run DPS: ~100% (high damage, more deaths)

Rogue: Spike damage with crits, glass cannon
  Target DPS: 100-110% of baseline (average), 200%+ on crits
  Survivability: 80-90% of baseline (dodge compensates)
  Effective run DPS: ~100% (crit spikes + dodge uptime)

Cleric: Low personal DPS, but sustains the run
  Target DPS: 60-70% of baseline
  Survivability: 160-180% of baseline (self-healing)
  Effective run DPS: ~100% (slow but steady, fewer failed runs)
```

### 8.4 Class Synergy Testing (if multiplayer/party system)

If the game supports party play or AI companions:

```
Best party compositions should be diverse, not stacked:

VIABLE:  Warrior + Mage + Rogue + Cleric (classic balanced)
VIABLE:  Warrior + Warrior + Mage + Cleric (double tank)
VIABLE:  Mage + Mage + Rogue + Cleric (glass cannon)
VIABLE:  Warrior + Rogue + Rogue + Cleric (burst comp)

NOT VIABLE (by design, should struggle):
  4x Warrior (no AoE, no range, no healing)
  4x Mage (no tank, mana starvation)
  4x Rogue (no healing, no AoE, no tank)
  4x Cleric (no damage, timer issues)
```

### 8.5 Per-Class Balance Checklist

**For EACH of the 4 classes, verify:**

#### Combat Feel
- [ ] Basic attack feels responsive and satisfying
- [ ] Abilities have clear visual/audio feedback
- [ ] Damage numbers feel proportional to effort
- [ ] Kill speed on trash mobs: 1-2 hits
- [ ] No ability feels "useless" (>10% usage in playtests)
- [ ] No ability feels "mandatory" (>80% of damage in playtests)
- [ ] Rotation/combo flow feels natural, not button-mashy

#### Stat Scaling
- [ ] Level-up feels meaningful (noticeable power increase)
- [ ] Primary stat scales damage proportionally
- [ ] Secondary stat provides complementary benefit
- [ ] No stat breakpoints that create "must reach" thresholds
- [ ] No stat that becomes irrelevant at any point in a run

#### Item Interaction
- [ ] Class benefits from at least 60% of item pool
- [ ] Class has 2-3 "dream items" that define peak builds
- [ ] No item makes any class invincible
- [ ] No item makes any class useless
- [ ] Weapon variety: at least 3 viable weapon types per class

#### Progression Curve
- [ ] Class feels weak but capable at Floor 1
- [ ] Class feels build identity forming by Floor 5
- [ ] Class feels powerful by Floor 10
- [ ] Class feels peak by Floor 13-15
- [ ] Power gains are smooth, no dead zones of 3+ floors with no upgrade

#### Difficulty Scaling
- [ ] Class can complete Easy with moderate skill
- [ ] Class can complete Normal with good skill
- [ ] Class can complete Hard with excellent skill
- [ ] No class has a difficulty where it CANNOT succeed

### 8.6 The "Doubling and Halving" Tuning Method

When something is imbalanced, don't tweak by 5%. That takes forever. Use multiplicative tuning:

```
1. Identify the imbalance
2. Double or halve the offending value
3. Test -- did we overshoot?
4. If yes, split the difference (go to the midpoint)
5. Repeat until balanced (binary search)

Example:
  Warrior does 100 damage, should do ~75
  Step 1: Halve to 50 -- too low
  Step 2: Midpoint: 75 -- feels right
  Step 3: Fine-tune: 72-78 range
  Done in 3 iterations, not 15
```

### 8.7 Final Balance Audit Checklist

Before any release (alpha, beta, launch), verify:

- [ ] All 4 classes have win rates within 10% of each other
- [ ] Average run time is within target (20-30 min)
- [ ] No floor has >30% death rate (indicates spike)
- [ ] No floor has <5% death rate after Floor 3 (indicates triviality)
- [ ] Boss death rates match targets (Section 4.5)
- [ ] Economy produces intended gold curve
- [ ] XP curve produces intended level-up cadence
- [ ] Pity systems trigger and function correctly
- [ ] No known exploits (infinite gold, damage, healing)
- [ ] All difficulty modes produce distinct experiences
- [ ] Accessibility options function without breaking balance
- [ ] Analytics tracking is live and reporting correctly
- [ ] 10+ complete playthroughs per class documented
- [ ] Edge case scenarios tested (Section 7.8)
- [ ] Performance targets met on all supported devices
- [ ] Mobile-specific tests passed (Section 7.4)

---

## Reference Sources

### Game Balance Theory
- David Sirlin, [Balancing Multiplayer Games Part 1: Definitions](https://www.sirlin.net/articles/balancing-multiplayer-games-part-1-definitions)
- David Sirlin, [Balancing Multiplayer Games Part 2: Viable Options](https://www.sirlin.net/articles/balancing-multiplayer-games-part-2-viable-options)
- Ian Schreiber & Brenda Romero, "Game Balance" (Routledge)
- Ian Schreiber, [Game Balance Concepts blog](https://gamebalanceconcepts.wordpress.com/)

### Roguelike Balance
- Mega Crit Games (Slay the Spire), [GDC 2019: Metrics Driven Design and Balance](https://www.gdcvault.com/play/1025731/-Slay-the-Spire-Metrics)
- Mega Crit Games, [How Slay the Spire's devs use data to balance](https://www.gamedeveloper.com/design/how-i-slay-the-spire-i-s-devs-use-data-to-balance-their-roguelike-deck-builder)
- Supergiant Games (Hades), [God Mode design philosophy](https://www.inverse.com/gaming/hades-god-mode-interview)
- Hades Wiki, [Pact of Punishment](https://hades.fandom.com/wiki/Pact_of_Punishment)

### RPG Combat Math
- [The Simplest Non-Problematic Damage Formula](https://tung.github.io/posts/simplest-non-problematic-damage-formula/)
- [Damage Formulas analysis](https://yujiri.xyz/game-design/damage-formulas.gmi)
- [You Smack The Rat for ??? Damage](https://jmargaris.substack.com/p/you-smack-the-rat-for-damage)
- [A Bestiary of Functions for Systems Designers](https://brunodias.dev/2021/03/19/functions-for-system-designers.html)

### Stat Scaling & Progression
- Davide Aversa, [GameDesign Math: RPG Level-based Progression](https://www.davideaversa.it/blog/gamedesign-math-rpg-level-based-progression/)
- SinisterDesign, [Designing RPG Mechanics for Scalability](https://sinisterdesign.net/designing-rpg-mechanics-for-scalability/)
- Ian Schreiber, [Level 7: Advancement, Progression and Pacing](https://gamebalanceconcepts.wordpress.com/2010/08/18/level-7-advancement-progression-and-pacing/)

### Difficulty Design
- Supergiant Games, [Hades God Mode origins](https://caniplaythat.com/2021/08/11/hades-god-mode-explained-by-supergiant-games/)
- [Balancing Inverse Difficulty Curves in Game Design](https://www.gamedeveloper.com/design/balancing-inverse-difficulty-curves-in-game-design)
- [Dynamic Game Difficulty Balancing](https://en.wikipedia.org/wiki/Dynamic_game_difficulty_balancing)

### Loot & Economy
- [Loot Drop Rates Calculation Guide](https://pulsegeek.com/articles/loot-drop-rates-calculation-guide-numbers-to-feel/)
- [Loot Drop Best Practices](https://www.gamedeveloper.com/design/loot-drop-best-practices)
- [Game Economy Design in Free-to-Play Games](https://machinations.io/articles/game-economy-design-free-to-play-games)

### Mobile QA
- [Mobile Game Testing Checklist](https://www.alphabin.co/blog/mobile-game-testing-checklist)
- [Balancing Turn-Based RPGs: The Big Picture](https://gamedevelopment.tutsplus.com/articles/balancing-turn-based-rpgs-the-big-picture--gamedev-8286)

---

> *"There is no greater honor than to ship a balanced game. There is no greater dishonor than to ship one that is not."*
> -- Worf, son of Mogh, QA Lead, Forge&Code
