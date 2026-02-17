# Game Dev Team — Skills & Learnings Summary

*Compiled from active development of Oasis (roguelike dungeon crawler for iOS)*
*Studio: Forge&Code | Engine: Godot 4.4+ | Model: Buy Once, Own Forever*

---

## What This Is

This document captures the hard-won knowledge from building a real game from scratch. Six specialist disciplines, one project, everything that actually mattered distilled here. Read this first. Dive into the full knowledge files per discipline after.

---

## The Six Disciplines

| Specialist | Domain | Key File |
|---|---|---|
| **Spock** | Game Direction & Production | `spock-game-director.md` |
| **Geordi** | Systems Architecture | `geordi-game-architect.md` |
| **Scotty** | Implementation (GDScript / Godot) | `scotty-game-developer.md` |
| **Worf** | Balance, QA, Release Gates | `worf-game-balance.md` |
| **Vic** | Music Composition & Audio Design | `vic-game-audio.md` |
| **Quark** | App Store & Marketing | `quark-game-marketing.md` |

---

## The Six Frameworks (Master These First)

### 1. Spock's Vision Test — Feature Filter

Every proposed feature passes 5 gates or gets cut:

```
1. Does this serve a design pillar?         → If NO, cut it.
2. Does this make the core loop better?     → If NO, defer it.
3. Can the player tell it's there?          → If NO, simplify it.
4. Is this the simplest version that works? → If NO, reduce it.
5. Would we miss it if we cut it?           → If NO, cut it.
```

Use this in every sprint to prevent scope creep. If a feature can't pass all 5, it doesn't ship.

---

### 2. Geordi's Three-Layer Architecture — Always Balance These

```
Performance    ←→    Flexibility    ←→    Simplicity
   (fps)             (iteration)          (maintainability)
```

**The rule:** Start simple. Add patterns only when pain emerges. Architecture that ships > perfect architecture that doesn't.

Godot-specific: the scene tree is already a composition system. Don't re-invent it. Ask: "Does Godot already do this for me?" before adding any pattern.

**When to use patterns:**

| Pattern | Use When |
|---|---|
| Component Node | Always — Godot's native model |
| Observer / Signals | Event-driven decoupling (UI, audio, achievements) |
| State Machine | 3+ states with defined transitions (character controller, AI, game flow) |
| Object Pool | 100+ frequent spawn/despawn (projectiles, particles, damage numbers) |
| Singleton (Autoload) | Truly global: AudioManager, SaveManager, EventBus |

---

### 3. Scotty's Component Pattern — The Foundation of Every Entity

Structure every entity as a composition of isolated, signal-based components:

```
Player (CharacterBody2D)
  ├── HealthComponent      # Tracks HP, emits health_changed / died
  ├── HurtboxComponent     # Receives damage
  ├── HitboxComponent      # Deals damage
  ├── KnockbackComponent   # Applies knockback physics
  ├── AbilityController    # Manages ability hotbar
  └── StatusEffectComponent # Buffs/debuffs
```

**Component rules (non-negotiable):**
1. Components don't know what they're attached to — works on Player, Enemy, or destructible barrel
2. Communicate via signals only — never directly access siblings or parents
3. One concern per component — HealthComponent tracks HP; DamageComponent deals it
4. All config via `@export` — inspectable from the editor without touching code

**GDScript file structure order** (follow this every time):
```
@tool / @icon → class_name → extends → docstring →
signals → enums → constants → static vars → @export vars →
public vars → private vars (_prefix) → @onready vars →
_ready/_process/_physics_process → public methods → private methods
```

---

### 4. Worf's Balance Metrics — What Actually Matters

**The Sirlin Definition:** A game is balanced if a *reasonably large number of options are viable*, especially in expert play.

**The two numbers that matter most:**
- **Pick rate** — How often players choose an option. Too low = it doesn't exist in your game.
- **Win rate correlation** — How often it appears in winning runs. Too high = overpowered. Too low = trap.

Track these per class/build from the first alpha build. Don't wait until content is complete.

**Damage formula recommendation (roguelikes):**

Avoid simple subtraction (`damage = attack - defense`) — enemies can become immune at high defense values.

Use the **Effective Health / Multiplicative Inverse** formula:
```
damage = attack × (100 / (100 + defense))
```
- Never reaches zero damage
- Each DEF point = 1% more effective HP (linear, predictable)
- Diminishing returns prevent runaway defense stacking
- Used by: League of Legends, Dota 2

**QA Release Gate:** Nothing ships without Worf's sign-off. Build a checklist and enforce it. Every. Single. Build.

---

### 5. Vic's Adaptive Audio — What Makes Game Music Work

**Study these composers for specific techniques:**

| Composer | Game | Key Technique to Steal |
|---|---|---|
| Koji Kondo | Mario / Zelda | Melody-first. If you can't hum it after one listen, simplify it |
| Nobuo Uematsu | Final Fantasy | Leitmotifs — assign themes to characters, transform them across the game |
| Darren Korb | Hades / Bastion | Pseudo-looping (never loop exactly), additive layering for intensity |
| Mick Gordon | DOOM 2016 | Music as reward for player behavior — aggressive play triggers full mix |
| Christopher Larkin | Hollow Knight | Minimal instrumentation — constraint forces every note to matter |

**Adaptive music implementation (Godot):**
- Vertical remixing: same timeline, add/remove stem tracks based on game state
- Horizontal resequencing: queue different cues based on what's happening
- Never loop exactly — rhythmic irregularity prevents listener fatigue
- Map instruments to locations — harp/marimba for nature, organ for magic, metal for bosses

**The most important rule:** Music and SFX are one cohesive soundscape. Design them together, not separately.

---

### 6. Quark's ASO Stack — How Players Find Your Game

Discovery is a funnel. Every step from keyword search to download is an engineering problem.

**The metadata fields that actually get indexed:**

| Field | Chars | Indexed? | Priority |
|---|---|---|---|
| App Name | 30 | Yes (highest weight) | Brand + primary keyword |
| Subtitle | 30 | Yes (high weight) | Secondary keywords + value prop |
| Keyword Field | 100 | Yes | Broad coverage, no repeats |
| Description | 4,000 | No | Conversion copy only |

**App Name format:** `Brand Name - Primary Keyword Phrase`
Example: `Oasis - Dungeon Crawler RPG` (27 chars)

**Keyword field rules:**
- No spaces after commas
- Don't repeat words from title/subtitle (already indexed)
- No plurals (Apple handles automatically)
- No filler words ("the", "a", "and")
- Single words beat phrases — Apple creates combinations

**Budget reality for $2.99 indie games:**
- Sweat equity (content, community, relationships) is the primary investment
- $2.99 × 70% (Apple cut) = ~$2.09/sale after standard commission
- Community IS the marketing — 500 genuine fans outperform $50K in ads

---

## Hard-Won Lessons From Building Oasis

### On Direction

**The director's daily five questions:**
1. Is the game fun right now? If not, what's the shortest path to fun?
2. What's the biggest risk to shipping? (Technical / Scope / Morale)
3. Is anyone blocked? Blocked people burn time.
4. Are we building the right thing? (Not "building it right" — that's engineering)
5. What would I cut if we had to ship tomorrow? This reveals true priorities.

**The Two-Hat Rule:** Dream first (no constraints). Evaluate second (hard constraints). Never wear both at once — it kills good ideas before they're formed.

**Wrong decision > no decision.** The team unblocks immediately. Adjust when you learn more.

---

### On Architecture

**Composition beats inheritance, always:**
```
BAD:  Enemy → FlyingEnemy → FlyingShootingEnemy → FlyingShootingPoisonEnemy
GOOD: Entity + [MoveComponent] + [ShootComponent] + [PoisonComponent] + [FlyComponent]
```

**Godot-specific gotchas:**
- Don't implement ECS in GDScript — Godot's node system already is a composition system; ECS only helps at 10,000+ homogeneous entities
- Don't use Command Pattern — GDScript's `Callable` type handles function-passing natively
- Don't implement Object Pool unless you have 100+ simultaneous spawns — reference counting, no GC pauses
- Plain classes (not Nodes) for pure data: item definitions, room data, stat blocks, algorithms

---

### On Implementation

**Autoload (singleton) good candidates:**
- `Events` — Global signal hub
- `AudioManager` — Survives scene changes
- `GameState` — Run state, player data
- `SaveManager` — Persistence layer

**Autoload bad candidates:**
- Level-specific logic
- Entity management
- Anything that should reset between scenes

**Save system pattern (proven):**
- Checkpoint-based saves at intentional points (portals, dungeon complete, death)
- Event-driven progress tracking (`report_kill()`, `report_collect()`, `report_dungeon_cleared()`)
- No polling in `_process()` — connect to signals instead

**Multi-portal / multi-region pattern:**
```gdscript
var _nearest_portal: Area2D = null

func _physics_process(_delta: float) -> void:
    # Check all portals, assign nearest
    if dist_to_portal1 < 80: _nearest_portal = portal1
    elif dist_to_portal2 < 80: _nearest_portal = portal2
    else: _nearest_portal = null

func _on_enter_pressed() -> void:
    if _nearest_portal: _nearest_portal.enter()
```
This pattern scales to N portals/dungeons without modification.

---

### On Balance

**Roguelike balance hierarchy:**
1. Fix anything that trivializes an entire run (overpowered)
2. Fix anything that makes a run unwinnable (trap options)
3. Improve clarity of weak options before nerfing strong ones
4. Diversify after everything is viable

**Track from first alpha:**
- Win rate per class per difficulty
- Ability usage rates
- Floor where most deaths occur
- Most/least used items

**Don't balance by theory alone.** Spreadsheets suggest; playtests decide.

---

### On Audio

**The single most important audio rule:**
> Music and sound effects are one cohesive soundscape. Design them together.

**Practical adaptive music setup (Godot 4):**
- Use `AudioStreamPlayer` with multiple bus channels per stem
- Signal-based mixing — `CombatStarted.emit()` → fade in percussion stem
- 4-stem minimum: melody, harmony, rhythm, bass
- Every track = multiple recorded versions for variation

---

### On Marketing

**Launch is 90 days:**
- Pre-launch: Build community, generate wishlists, seed reviewers
- Launch week: Capture attention, drive installs, get early reviews
- Post-launch: Updates sustain momentum, each update = re-promotion opportunity

**The only metric that matters at indie scale:** Revenue per dollar of attention spent. Track it from day one.

---

## What We Built in Oasis (Reference Implementation)

| System | Implementation | Status |
|---|---|---|
| Combat | D&D 5e base (d20 rolls vs AC, saving throws, action economy) | DONE |
| Quest System | Event-driven, `report_kill/collect/clear`, HUD cycling 4s | DONE |
| Save/Load | Checkpoint-based, `user://oasis_save.dat` | DONE |
| Inventory | 20 slots, loot drops from enemies, equipment slots | DONE |
| Dungeons | Forest + Ice Caves, BSP + cellular automata hybrid | DONE |
| Hub World | Multi-portal, NPCs with dialogue, respawn point | DONE |
| Audio | 4-stem adaptive combat/ambient crossfade, SFX pooling | DONE |
| VFX | Particle effects, screen shake, dynamic shadows | DONE |
| UI | Virtual joystick, ability hotbar, quest HUD, dialogue | DONE |
| Proc Gen | BSP dungeon layout + cellular automata room fill | IN PROGRESS |

**Build pipeline (Godot → iOS device):**
```bash
# 1. Export pack
$GODOT --headless --path $PROJECT/godot-game/ --export-pack "iOS" $PROJECT/ios-app/oasis.pck

# 2. Generate Xcode project
xcodegen generate

# 3. Build
xcodebuild -scheme OasisPrototype -destination 'generic/platform=iOS'

# 4. Install to device
xcrun devicectl device install app --device $DEVICE $APP_PATH
```

---

## The Non-Negotiable Rules

1. **Prototype first, polish last** — Core loop must be fun before any polish is added
2. **Start simple, add complexity when it hurts** — Premature abstraction is technical debt
3. **Ship gates are sacred** — QA sign-off before every build that goes to players
4. **Design pillars are law** — If a feature doesn't serve a pillar, it doesn't exist
5. **Play your own game daily** — If the director isn't playing it, nobody else will
6. **Community first, algorithm second** — 500 real fans > any ad spend at $2.99

---

## Recommended Reading Order

**If you're new to game dev:**
1. `spock-game-director.md` — Understand what direction actually is
2. `worf-game-balance.md` — Understand what "fun" actually means mathematically
3. `scotty-game-developer.md` — Learn the Godot patterns

**If you're technical and want to ship:**
1. `geordi-game-architect.md` — Systems architecture
2. `scotty-game-developer.md` — Implementation patterns
3. `quark-game-marketing.md` — How people find your game

**If you're building audio:**
1. `vic-game-audio.md` — Read the whole thing
2. Study Darren Korb (Hades) for adaptive music specifically

---

*Built by the Forge&Code Starfleet crew during Oasis development.*
*Share freely. Credit appreciated. Don't sell it. Don't claim it as yours.*
*License: Share Alike — see SHARING.md for full terms.*
