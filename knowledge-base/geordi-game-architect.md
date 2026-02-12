# Game Architecture Knowledge Base
## Technical Architect Domain — Forge&Code / Godot 4.x

**Compiled:** 2026-02-12
**Scope:** Complete architectural reference for designing scalable game systems in Godot 4.x, targeting mobile-first (iOS via SwiftGodotKit), PC, Mac, and eventual console deployment.
**Primary Project:** OASIS — a multi-game RPG platform with procedural generation, portal-based world topology, and a D&D campaign pipeline.

---

## Table of Contents

1. [Foundational Game Architecture Patterns](#1-foundational-game-architecture-patterns)
2. [Godot-Specific Architecture](#2-godot-specific-architecture)
3. [Data-Driven Design](#3-data-driven-design)
4. [Procedural Generation Architecture](#4-procedural-generation-architecture)
5. [Save System Architecture](#5-save-system-architecture)
6. [Game State Management](#6-game-state-management)
7. [Cross-Platform Architecture](#7-cross-platform-architecture)
8. [Performance Architecture](#8-performance-architecture)
9. [Plugin and Mod Architecture](#9-plugin-and-mod-architecture)
10. [Networking Architecture](#10-networking-architecture)
11. [OASIS-Specific Recommendations](#11-oasis-specific-recommendations)

---

## 1. Foundational Game Architecture Patterns

### 1.1 Pattern Catalog (from Game Programming Patterns by Robert Nystrom)

The following patterns are the canonical vocabulary for game architecture. Know when to reach for each.

#### Design Patterns Revisited (Classic GoF Applied to Games)

| Pattern | Game Use Case | When to Use | When NOT to Use |
|---------|--------------|-------------|-----------------|
| **Command** | Input remapping, undo/redo, replay | Player input systems, AI command queues, turn-based replays | Simple input with no remapping needs; GDScript's first-class functions often suffice |
| **Flyweight** | Shared intrinsic state | Tile data, particle textures, item definitions — anything with thousands of instances sharing common data | Godot Resources already implement this natively |
| **Observer** | Event-driven decoupling | Achievement systems, UI updates, audio triggers, analytics | When you need temporal decoupling (use Event Queue instead) |
| **Prototype** | Spawning varied instances | Enemy spawners, bullet factories, procedural entity generation | Godot scenes + `instantiate()` already implement this |
| **Singleton** | Global access points | Audio manager, input manager, save manager | Overuse creates hidden coupling; prefer dependency injection where practical |
| **State** | Behavior switching | Character controllers, AI behaviors, game flow, UI screens | Fewer than 3 states (use a boolean); no transitions over time (use conditionals) |

#### Sequencing Patterns

| Pattern | Game Use Case | Key Insight |
|---------|--------------|-------------|
| **Double Buffer** | Render state, physics state | Prevents tearing between update and display; Godot handles this internally for rendering but the concept applies to game state snapshots for networking |
| **Game Loop** | Frame management | Godot provides this via `_process()` and `_physics_process()`; understand fixed vs variable timestep for deterministic systems |
| **Update Method** | Per-frame entity behavior | The foundation of `_process(delta)` — each entity updates itself. Be aware of update ordering dependencies |

#### Behavioral Patterns

| Pattern | Game Use Case | Key Insight |
|---------|--------------|-------------|
| **Bytecode** | Moddable behaviors | Define a virtual machine for game behaviors — enables modding without exposing engine internals. Relevant for OASIS's DM pipeline |
| **Subclass Sandbox** | Varied behaviors sharing a base | Define base operations (spawn particle, play sound, apply damage) that concrete abilities/spells combine. Perfect for OASIS's ability system |
| **Type Object** | Data-driven entity types | Instead of a class per monster type, define monster "types" as data objects. Directly applicable to OASIS's bestiary and item databases |

#### Decoupling Patterns

| Pattern | Game Use Case | Key Insight |
|---------|--------------|-------------|
| **Component** | Entity composition | The foundation of Godot's node system. Behavior defined by attached components, not class hierarchy |
| **Event Queue** | Asynchronous events | Ring-buffer queue decouples sender from receiver in TIME (not just identity like Observer). Critical for audio, networking, AI perception |
| **Service Locator** | Accessing global systems | Alternative to Singleton that enables swapping implementations (real audio vs null audio for testing). Godot autoloads partially serve this role |

#### Optimization Patterns

| Pattern | Game Use Case | Key Insight |
|---------|--------------|-------------|
| **Data Locality** | Cache-friendly iteration | Arrange homogeneous data in contiguous arrays for cache-friendly processing. Godot's server APIs use this internally |
| **Dirty Flag** | Expensive recomputation | Only recompute derived values (world transforms, pathfinding, visibility) when inputs change. Built into Godot's transform system |
| **Object Pool** | Frequent spawn/despawn | Pre-allocate and recycle objects to avoid allocation spikes. Critical for particles, projectiles, damage numbers, enemies in OASIS |
| **Spatial Partition** | Broad-phase queries | Divide the world for efficient lookups. Godot provides physics layers and Area2D/3D for this |

### 1.2 The Three Pillars of Game Architecture

Every architectural decision in a game boils down to balancing these three concerns:

1. **Performance** — Frame budget is sacred. 60fps on mobile means 16.6ms per frame. Every system must respect this.
2. **Flexibility** — Games change constantly during development. Architecture must accommodate iteration without rewrites.
3. **Simplicity** — The simplest code that solves the problem wins. Patterns come at a cost; never add complexity without a clear benefit.

**The Rule:** Start simple. Add patterns when pain emerges. The best architecture is the one that lets you ship.

### 1.3 Composition Over Inheritance

This is the single most important architectural principle for game development:

```
BAD:  Enemy → FlyingEnemy → FlyingShootingEnemy → FlyingShootingPoisonEnemy
GOOD: Entity + [MovementComponent] + [ShootComponent] + [PoisonComponent] + [FlyComponent]
```

Inheritance creates rigid taxonomies that break when designers want combinations. Composition allows mixing any set of behaviors onto any entity. Godot's node system is built for this — use it.

### 1.4 When Simplicity Beats Patterns

From GDQuest's practical guidance on patterns in Godot:

- **Don't use Object Pooling in GDScript** unless you have hundreds of simultaneous spawns. GDScript uses reference counting, not garbage collection — there are no GC pauses.
- **Don't implement ECS** unless you're processing tens of thousands of homogeneous entities. Godot's node system already provides composition.
- **Don't use the Command Pattern** when GDScript's `Callable` type handles function-passing natively.
- **Don't use Data Locality** in GDScript — the engine's internal servers already handle cache-friendly processing.

**Ask before implementing any pattern:** "Does Godot already do this for me?"

---

## 2. Godot-Specific Architecture

### 2.1 Scene Tree: Godot's Core Architecture

Godot's scene tree is not a limitation — it's a feature. Juan Linietsky (Godot creator) deliberately chose this over ECS because:

1. **Explicit relationships** — The tree visually shows what every entity is composed of. No hidden systems, no implicit queries.
2. **Composition at a higher level** — Where ECS requires Transform + RigidBody + Collider + Sprite as separate components, Godot's `CharacterBody2D` node composes these with automatic transform inheritance.
3. **Performance where it matters** — Physics, rendering, and audio run as separate server systems with data-oriented optimizations internally. Nodes are high-level interfaces to this optimized processing.

**When the scene tree is sufficient (most cases):**
- Fewer than ~1000 active game entities
- Entities have diverse behaviors (RPG characters, items, NPCs, UI elements)
- Entities need visual editor support

**When to consider ECS-style approaches:**
- Thousands to tens of thousands of homogeneous entities (bullet hell, particle simulations, large-scale RTS)
- Data that needs to be processed in bulk with cache-friendly iteration
- Via GDExtension (C++/Rust), NOT in GDScript — ECS in a scripting language has no cache benefit

### 2.2 Scene Composition Patterns

#### The Component Node Pattern

The most important Godot pattern. Create reusable behavior nodes that attach to entities:

```
Player (CharacterBody2D)
  ├── Sprite2D
  ├── CollisionShape2D
  ├── HealthComponent (Node)        # Tracks HP, emits damage/heal signals
  ├── HurtboxComponent (Area2D)     # Receives damage
  ├── HitboxComponent (Area2D)      # Deals damage
  ├── KnockbackComponent (Node)     # Applies knockback velocity
  ├── AbilityController (Node)      # Manages ability hotbar and cooldowns
  └── StatusEffectComponent (Node)  # Tracks active buffs/debuffs
```

**Design rules for components:**
1. **Isolation** — Components must not know about their parent's type. A `HealthComponent` works on a Player, Enemy, or Destructible Barrel.
2. **Signal-based communication** — Components emit signals, never directly modify siblings or parents.
3. **Single responsibility** — One component, one concern. `HealthComponent` tracks HP. `DamageComponent` deals damage. They communicate through signals, not shared state.
4. **Inspectable API** — Use `@export` variables for designer-tunable values. Components should be configurable from the Godot editor.

```gdscript
# HealthComponent.gd
class_name HealthComponent
extends Node

signal health_changed(current: float, maximum: float)
signal died

@export var max_health: float = 100.0
var current_health: float

func _ready() -> void:
    current_health = max_health

func take_damage(amount: float) -> void:
    current_health = maxf(current_health - amount, 0.0)
    health_changed.emit(current_health, max_health)
    if current_health <= 0.0:
        died.emit()

func heal(amount: float) -> void:
    current_health = minf(current_health + amount, max_health)
    health_changed.emit(current_health, max_health)
```

#### Reusable Scene Instancing

Scenes are Godot's Prototype pattern. Any scene can be instanced multiple times with different configurations:

```gdscript
# Spawner that creates enemies from a scene template
@export var enemy_scene: PackedScene
@export var spawn_count: int = 5

func spawn_wave() -> void:
    for i in spawn_count:
        var enemy := enemy_scene.instantiate() as CharacterBody2D
        enemy.position = _get_spawn_position(i)
        add_child(enemy)
```

### 2.3 Autoload (Singleton) Patterns

Autoloads are Godot's global singletons. Use them judiciously.

**Good candidates for autoloads:**
- `Stats` — Player stats (single player, globally accessed)
- `SaveManager` — Save/load operations
- `SceneManager` — Scene transition logic
- `AudioManager` — Sound playback management
- `ItemDatabase`, `AbilityDatabase` — Read-only data registries

**Bad candidates for autoloads:**
- Anything that has per-instance state (use scene nodes instead)
- Systems that only one scene needs (make it a child node of that scene)
- Anything that creates tight coupling between unrelated systems

**The autoload test:** "Does literally every scene in the game need access to this?" If yes, autoload. If no, inject it or make it local.

**Current OASIS autoloads (14 registered):**
Stats, ItemDatabase, Inventory, SceneManager, HitStop, CameraShake, AudioManager, QuestManager, SaveManager, EnemyPool, StatusEffectDatabase, AbilityDatabase, ClassDatabase, RaceDatabase

This count is reasonable for a single-player RPG. As the project grows, consider grouping related autoloads:
- **GameData** — Consolidate Stats + Inventory + QuestManager (player state)
- **ContentDB** — Consolidate ItemDatabase + AbilityDatabase + ClassDatabase + RaceDatabase + StatusEffectDatabase (read-only data)
- **GameServices** — Keep SaveManager, SceneManager, AudioManager separate (distinct responsibilities)

### 2.4 Signal Bus Pattern (Event Bus)

A centralized event emitter for cross-system communication without direct coupling.

```gdscript
# events.gd — Autoload as "Events"
extends Node

# Combat events
signal enemy_killed(enemy_type: String, position: Vector2)
signal damage_dealt(source: Node, target: Node, amount: float)
signal player_died

# Economy events
signal item_acquired(item_id: String, quantity: int)
signal gold_changed(new_amount: int)

# Progression events
signal level_up(new_level: int)
signal quest_completed(quest_id: String)

# UI events
signal show_dialogue(npc_name: String, text: String)
signal show_notification(message: String)
```

**Usage:**
```gdscript
# Emitter (enemy death script)
Events.enemy_killed.emit(enemy_type, global_position)

# Listener (achievement system)
func _ready() -> void:
    Events.enemy_killed.connect(_on_enemy_killed)
```

**Rules:**
1. Use the event bus for truly global events (enemy killed, level up, quest complete).
2. Use direct signals for parent-child communication within a scene.
3. Don't overuse — if debugging becomes hard because you can't trace event flow, you've used too many global events.

### 2.5 Three-Layer Architecture for Godot

Adapted from Chickensoft's game architecture recommendations:

```
┌─────────────────────────────────────┐
│  Layer 1: Visual Layer (Nodes)      │  Godot scenes, sprites, UI, animation
│  - Scripts attached to scene nodes  │  Responds to state, emits input
│  - No business logic here           │
├─────────────────────────────────────┤
│  Layer 2: Logic Layer               │  State machines, game rules, AI
│  - Pure game logic (testable)       │  Processes input, produces state
│  - Visual game logic (state→visuals)│  Translates logic state to visual commands
├─────────────────────────────────────┤
│  Layer 3: Data Layer                │  Save files, databases, network
│  - Resources and serialization      │  Provides data to logic layer
│  - Network clients (future)         │
└─────────────────────────────────────┘
```

**The rule:** Objects in one layer can only depend on the layer directly below them. Never upward, never skipping layers.

### 2.6 GDScript Best Practices for Architecture

1. **Always use static typing** — Enables autocomplete, compile-time errors, and slight performance gains:
   ```gdscript
   var speed: float = 200.0           # Explicit
   var direction := Vector2.ZERO      # Inferred with :=
   ```

2. **Use `class_name` for all reusable scripts** — Makes them available in the editor and enables type-safe references.

3. **Prefer `@export` over hardcoded values** — Every tunable value should be designer-accessible.

4. **Use groups for cross-scene queries** — `get_tree().get_nodes_in_group("enemy")` is Godot's lightweight tag system.

5. **Use `@onready` for child node references** — Cache references once, avoid repeated lookups:
   ```gdscript
   @onready var health: HealthComponent = $HealthComponent
   @onready var sprite: Sprite2D = $Sprite2D
   ```

---

## 3. Data-Driven Design

### 3.1 The Principle

**Define game content as data, not code.** Abilities, items, enemies, status effects, quests, and loot tables should be declared as data that the engine reads, not as hardcoded class hierarchies.

Benefits:
- **Designers can add content** without touching code
- **Content is serializable** — save/load, network sync, and procedural generation become trivial
- **Balance changes don't require code changes** — tune numbers in data files, not scripts
- **Modding becomes possible** — external data files can extend or override base content

### 3.2 Godot Custom Resources (The Core Mechanism)

Custom Resources are Godot's equivalent of Unity's ScriptableObjects. They are the primary tool for data-driven design.

```gdscript
# item_data.gd
class_name ItemData
extends Resource

@export var id: String = ""
@export var display_name: String = ""
@export var description: String = ""
@export var icon: Texture2D
@export var rarity: int = 0  # 0=Common, 1=Magic, 2=Rare, 3=Legendary, 4=Set
@export var item_type: String = "consumable"  # weapon, armor, consumable, material
@export var stackable: bool = false
@export var max_stack: int = 1
@export var value: int = 0
```

```gdscript
# weapon_data.gd
class_name WeaponData
extends ItemData

@export var damage_min: float = 1.0
@export var damage_max: float = 5.0
@export var attack_speed: float = 1.0
@export var range: float = 32.0
@export var damage_type: String = "physical"  # physical, fire, ice, lightning, poison
@export var affixes: Array[AffixData] = []
```

```gdscript
# ability_data.gd
class_name AbilityData
extends Resource

@export var id: String = ""
@export var display_name: String = ""
@export var description: String = ""
@export var icon: Texture2D
@export var cooldown: float = 1.0
@export var mana_cost: float = 0.0
@export var damage_multiplier: float = 1.0
@export var range: float = 64.0
@export var area_of_effect: float = 0.0
@export var status_effects: Array[String] = []
@export var animation_key: String = ""
@export var sfx_key: String = ""
@export var vfx_scene: PackedScene
```

### 3.3 Resource-Based Content Databases

Instead of hardcoding content in autoload scripts, use Resource arrays or dictionaries:

```gdscript
# content_database.gd
class_name ContentDatabase
extends Resource

@export var items: Array[ItemData] = []
@export var abilities: Array[AbilityData] = []
@export var enemy_types: Array[EnemyData] = []
@export var status_effects: Array[StatusEffectData] = []

var _item_index: Dictionary = {}

func build_indices() -> void:
    _item_index.clear()
    for item in items:
        _item_index[item.id] = item

func get_item(id: String) -> ItemData:
    return _item_index.get(id)
```

**Advantages over dictionary-in-autoload approach:**
- Type-safe in the editor
- Version-controlled as `.tres` files
- Editable without running the game
- Multiple databases possible (base game + DLC + mods)

### 3.4 The Type Object Pattern for Game Entities

Instead of creating a GDScript class per enemy type, define enemy "types" as data:

```gdscript
# enemy_data.gd
class_name EnemyData
extends Resource

@export var id: String = ""
@export var display_name: String = ""
@export var base_health: float = 50.0
@export var base_damage: float = 10.0
@export var move_speed: float = 60.0
@export var xp_reward: int = 10
@export var loot_table_id: String = ""
@export var sprite_frames: SpriteFrames
@export var ai_behavior: String = "chase"  # chase, patrol, ranged, boss
@export var attack_range: float = 32.0
@export var attack_cooldown: float = 1.0
@export var resistances: Dictionary = {}
@export var abilities: Array[String] = []
```

The enemy scene becomes a single generic template that reads its `EnemyData` resource:

```gdscript
# generic_enemy.gd
extends CharacterBody2D

@export var enemy_data: EnemyData

func _ready() -> void:
    $HealthComponent.max_health = enemy_data.base_health
    $Sprite2D.sprite_frames = enemy_data.sprite_frames
    # Configure AI, attacks, etc. from data
```

This means new enemy types are created by making new `.tres` files in the editor — no code required.

### 3.5 Loot Table Architecture

For OASIS's Diablo-inspired loot system, loot tables should be pure data:

```gdscript
# loot_table.gd
class_name LootTable
extends Resource

@export var entries: Array[LootEntry] = []
@export var guaranteed_drops: Array[String] = []  # Item IDs always dropped
@export var drop_count_min: int = 1
@export var drop_count_max: int = 3

func roll(player_level: int, player_class: String, rng: RandomNumberGenerator) -> Array[String]:
    var results: Array[String] = []
    results.append_array(guaranteed_drops)
    var count := rng.randi_range(drop_count_min, drop_count_max)
    for i in count:
        var drop := _weighted_roll(player_level, player_class, rng)
        if drop != "":
            results.append(drop)
    return results
```

```gdscript
# loot_entry.gd
class_name LootEntry
extends Resource

@export var item_id: String = ""
@export var weight: float = 1.0
@export var min_level: int = 1
@export var max_level: int = 20
@export var class_affinity: Array[String] = []  # Empty = all classes
@export var rarity_override: int = -1  # -1 = use item's base rarity
```

### 3.6 Affix System for Procedural Items

For Diablo-style affix generation:

```gdscript
# affix_data.gd
class_name AffixData
extends Resource

@export var id: String = ""
@export var display_prefix: String = ""   # "Flaming" → Flaming Sword
@export var display_suffix: String = ""   # "of the Bear" → Sword of the Bear
@export var stat_modifiers: Dictionary = {}  # {"strength": 3, "fire_damage": 5}
@export var min_level: int = 1
@export var max_level: int = 20
@export var compatible_types: Array[String] = []  # weapon, armor, ring, etc.
@export var rarity_weight: Dictionary = {}  # {1: 10, 2: 5, 3: 2, 4: 1} per rarity tier
```

---

## 4. Procedural Generation Architecture

### 4.1 Dungeon Generation System Design

OASIS needs a modular, extensible dungeon generation pipeline. The architecture should separate concerns into independent, composable stages.

#### The Pipeline Architecture

```
┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│   Layout     │ ──> │   Content    │ ──> │   Polish     │
│   Generator  │     │   Populator  │     │   Pass       │
└──────────────┘     └──────────────┘     └──────────────┘
     │                      │                    │
     ├── Room placement     ├── Enemy spawns     ├── Lighting
     ├── Corridor routing   ├── Item placement   ├── Ambiance
     ├── Door placement     ├── Trap placement   ├── Decoration
     └── Region tagging     ├── NPC placement    └── Validation
                            └── Boss room setup
```

Each stage operates on a shared data structure (the dungeon grid) and is independently swappable.

#### The Dungeon Data Model

```gdscript
# dungeon_data.gd
class_name DungeonData
extends RefCounted

var width: int
var height: int
var tiles: Array[Array]  # 2D grid of TileType enum values
var rooms: Array[Rect2i]  # Room bounds
var corridors: Array[Array]  # Corridor tile positions
var doors: Array[Vector2i]  # Door positions
var spawn_point: Vector2i
var exit_point: Vector2i
var regions: Dictionary  # region_id → Array[Vector2i]
var metadata: Dictionary  # Arbitrary data for populators
var seed_value: int

enum TileType { WALL, FLOOR, DOOR, STAIRS_UP, STAIRS_DOWN, TRAP, WATER, PIT }
```

#### Layout Generator Interface

```gdscript
# layout_generator.gd
class_name LayoutGenerator
extends RefCounted

var rng: RandomNumberGenerator

func generate(config: DungeonConfig) -> DungeonData:
    rng = RandomNumberGenerator.new()
    rng.seed = config.seed_value
    # Subclasses implement this
    return _generate_layout(config)

func _generate_layout(_config: DungeonConfig) -> DungeonData:
    push_error("_generate_layout not implemented")
    return null
```

#### Interchangeable Layout Algorithms

**Binary Space Partition (BSP):**
- Recursively divide the space into rectangular rooms
- Produces orderly, dungeon-like layouts
- Best for: Crypts, castles, structured dungeons

**Random Walk (Drunkard's Walk):**
- Start from a point, walk randomly, carving floor tiles
- Produces organic, cave-like layouts
- Best for: Caves, natural environments

**Rooms and Mazes (Bob Nystrom's algorithm):**
1. Place non-overlapping rooms at random positions
2. Fill remaining space with maze corridors
3. Connect all regions via spanning tree
4. Optionally add loops (controlled randomness for multiple paths)
5. Remove dead-end corridors
- Best for: Classic dungeon-crawl layouts

**Wave Function Collapse (WFC):**
- Define tile adjacency rules, then collapse a grid respecting constraints
- Can combine with BSP for room-level layout + WFC for tile-level detail
- Best for: Complex themed tilesets, visually varied environments

**Graph-Based Generation:**
- Define the dungeon as a graph of interconnected nodes (rooms)
- Assign room types (combat, treasure, trap, boss, rest)
- Generate spatial layout from the graph
- Best for: Guaranteeing game-design constraints (boss is always at the end, treasure after combat)

#### Content Populator Interface

```gdscript
# content_populator.gd
class_name ContentPopulator
extends RefCounted

func populate(dungeon: DungeonData, config: DungeonConfig) -> void:
    var rng := RandomNumberGenerator.new()
    rng.seed = dungeon.seed_value + 1000  # Offset seed for content layer
    _place_enemies(dungeon, config, rng)
    _place_items(dungeon, config, rng)
    _place_traps(dungeon, config, rng)
    _place_boss(dungeon, config, rng)
```

### 4.2 Seeded Randomness Architecture

**Critical principle:** Every procedural system MUST use seeded `RandomNumberGenerator` instances, never global `randi()`/`randf()`.

```gdscript
# WRONG — non-reproducible
func generate_dungeon():
    var room_count = randi_range(5, 10)  # Uses global RNG state

# RIGHT — deterministic from seed
func generate_dungeon(seed_value: int):
    var rng := RandomNumberGenerator.new()
    rng.seed = seed_value
    var room_count = rng.randi_range(5, 10)  # Reproducible
```

**Seed architecture for OASIS:**

```
Master Seed (per dungeon run)
  ├── Layout Seed    = master_seed + 0       (room placement, corridors)
  ├── Content Seed   = master_seed + 1000    (enemy placement, items)
  ├── Loot Seed      = master_seed + 2000    (item generation, affixes)
  ├── Narrative Seed = master_seed + 3000    (NPC dialogue variants)
  └── Visual Seed    = master_seed + 4000    (decoration, lighting variation)
```

Each subsystem gets its own derived seed so changes to one layer don't cascade into others. Adding a new enemy spawn algorithm shouldn't change the dungeon layout.

**Seed storage for replay and sharing:**
```gdscript
# Players can share dungeon seeds
var run_data := {
    "seed": 42,
    "difficulty": 3,
    "dungeon_type": "cave",
    "player_level": 7,
}
# Any player with this data generates the exact same dungeon
```

### 4.3 Validation Pass

Every generated dungeon must pass validation before becoming playable:

1. **Connectivity** — All rooms are reachable from the spawn point (flood-fill test)
2. **Difficulty Curve** — Enemy density increases toward the boss room
3. **Completability** — No softlocks (required keys are accessible before their doors)
4. **Performance** — Total entity count doesn't exceed budget for target platform
5. **Boundary Check** — No rooms or corridors extend outside the grid

```gdscript
func validate(dungeon: DungeonData) -> Array[String]:
    var errors: Array[String] = []
    if not _is_connected(dungeon):
        errors.append("Dungeon has unreachable rooms")
    if not _has_path_to_exit(dungeon):
        errors.append("No path from spawn to exit")
    if _count_entities(dungeon) > MAX_ENTITIES:
        errors.append("Entity count exceeds budget: %d" % _count_entities(dungeon))
    return errors
```

### 4.4 DM-to-Dungeon Pipeline Architecture (OASIS-Specific)

For OASIS's D&D campaign pipeline:

```
DM Notes (structured/natural language)
  │
  ▼
[Parser Layer] ─────────────── Extracts entities, encounters, items, narrative
  │                             Output: DungeonConfig resource
  ▼
[Layout Generator] ──────────── Selects algorithm based on environment type
  │                             Uses theme to choose cave/crypt/forest/etc.
  ▼
[Content Populator] ─────────── Places enemies from bestiary, items from loot tables
  │                             Maps DM's encounters to game entities
  ▼
[Narrative Wrapper] ─────────── Generates flavor text, NPC dialogue, quest text
  │                             Maps DM's story beats to quest objectives
  ▼
[Theme Engine] ──────────────── Applies tileset, lighting, ambiance, music
  │                             Selects from theme library
  ▼
[Validator] ─────────────────── Connectivity, difficulty, completability checks
  │
  ▼
[Scene Builder] ─────────────── Converts DungeonData to Godot scene tree
                                Creates portal in hub world
```

---

## 5. Save System Architecture

### 5.1 Roguelike Save Philosophy

OASIS has two fundamentally different data categories:

**Persistent Data (across all runs):**
- Character identity (name, race, class, appearance)
- Character progression (level, XP, skill points spent)
- Inventory and equipment
- Currency and materials
- Completed quests and achievements
- Hub world state (unlocked areas, discovered portals)
- Settings and preferences

**Run Data (per dungeon session):**
- Current dungeon seed and configuration
- Current floor/room position
- Dungeon discovery state (fog of war, opened chests)
- Current HP/MP/cooldowns
- Temporary buffs/debuffs
- Enemies killed this run
- Loot acquired this run (not yet committed to inventory)

**The contract:** Persistent data survives death. Run data is lost on death (roguelike) or saved mid-run for session breaks.

### 5.2 Save Format Recommendation for OASIS

Use **`FileAccess.store_var()`** for the current phase. It's the best balance of simplicity, type support, and performance for a single-player mobile game.

**NOT JSON** — JSON doesn't natively support `Vector2`, `Color`, or `int` types. It requires manual conversion and is slower.

**NOT Custom Resources for save files** — While great for content databases, Resource save files can execute code when loaded, creating a security risk if saves are ever shared or synced.

**Consider migrating to Custom Resources** if/when the save structure becomes very complex and editor inspection becomes valuable for debugging.

### 5.3 Versioned Save Schema

OASIS already implements version-based migration. Here's the recommended architecture for scaling this:

```gdscript
# save_schema.gd
class_name SaveSchema
extends RefCounted

const CURRENT_VERSION := 3

# Migration chain — each function migrates ONE version forward
static var _migrations := {
    1: _migrate_v1_to_v2,
    2: _migrate_v2_to_v3,
}

static func migrate(data: Dictionary) -> Dictionary:
    var version: int = data.get("version", 1)
    while version < CURRENT_VERSION:
        if _migrations.has(version):
            data = _migrations[version].call(data)
        version += 1
    data["version"] = CURRENT_VERSION
    return data

static func _migrate_v1_to_v2(data: Dictionary) -> Dictionary:
    # Add wisdom and charisma stats
    # Add equipment slots
    # Add ability hotbar
    return data

static func _migrate_v2_to_v3(data: Dictionary) -> Dictionary:
    # Add hub world state
    # Add achievement tracking
    return data
```

**Migration rules:**
1. **Never remove fields** — Mark deprecated fields with a comment, but keep reading them for backward compatibility.
2. **Always provide defaults** — Use `.get("key", default_value)` for every field access.
3. **Additive changes only** — Adding new fields is safe. Renaming or restructuring requires a migration function.
4. **Test migrations** — Keep test saves from each version to verify the migration chain works end-to-end.
5. **Version number in save file** — Always the first thing read.

### 5.4 Auto-Save Architecture

For mobile, auto-save is critical — players get phone calls, switch apps, or run out of battery.

```gdscript
# Auto-save triggers:
# 1. After every dungeon floor transition
# 2. After every significant event (boss kill, quest complete)
# 3. On _notification(NOTIFICATION_APPLICATION_PAUSED) — iOS backgrounding
# 4. On _notification(NOTIFICATION_WM_CLOSE_REQUEST) — App closing
# 5. On a timer (every 60 seconds during gameplay)

func _notification(what: int) -> void:
    if what == NOTIFICATION_APPLICATION_PAUSED:
        SaveManager.save_game()
    elif what == NOTIFICATION_WM_CLOSE_REQUEST:
        SaveManager.save_game()
```

### 5.5 Save Slot Architecture

For OASIS's persistent character system:

```
user://
  ├── settings.cfg              # Global settings (audio, controls)
  ├── profiles/
  │   ├── profile_1.dat         # Character 1 persistent data
  │   ├── profile_2.dat         # Character 2 persistent data
  │   └── profile_3.dat         # Character 3 persistent data
  ├── runs/
  │   ├── active_run.dat        # Current dungeon run state (if any)
  │   └── run_history.dat       # Completed run statistics
  └── hub/
      └── hub_state.dat         # Hub world discovery, NPCs met, portals found
```

---

## 6. Game State Management

### 6.1 Finite State Machines (FSM)

The most important pattern for game flow and entity behavior.

#### Basic FSM for Character States

```gdscript
# state.gd — Base state class
class_name State
extends Node

signal transitioned(new_state_name: String)

func enter() -> void:
    pass

func exit() -> void:
    pass

func handle_input(_event: InputEvent) -> void:
    pass

func update(_delta: float) -> void:
    pass

func physics_update(_delta: float) -> void:
    pass
```

```gdscript
# state_machine.gd
class_name StateMachine
extends Node

@export var initial_state: State
var current_state: State

func _ready() -> void:
    for child in get_children():
        if child is State:
            child.transitioned.connect(_on_state_transitioned)
    if initial_state:
        initial_state.enter()
        current_state = initial_state

func _unhandled_input(event: InputEvent) -> void:
    if current_state:
        current_state.handle_input(event)

func _process(delta: float) -> void:
    if current_state:
        current_state.update(delta)

func _physics_process(delta: float) -> void:
    if current_state:
        current_state.physics_update(delta)

func _on_state_transitioned(new_state_name: String) -> void:
    var new_state: State = get_node_or_null(new_state_name)
    if new_state and new_state != current_state:
        current_state.exit()
        current_state = new_state
        current_state.enter()
```

#### When to Use Each State Machine Type

| Type | Use Case | OASIS Example |
|------|----------|---------------|
| **Basic FSM** | Simple behavior with clear state transitions | Enemy AI (idle → chase → attack → flee) |
| **Hierarchical FSM** | States share common behavior | Player movement (all ground states handle jumping; walk/run/slide inherit from OnGround) |
| **Pushdown Automata** | Need to return to previous state | Casting a spell while moving — push spell state, pop back to movement state when done |
| **Concurrent FSMs** | Independent state dimensions | Player locomotion FSM + Equipment FSM (walking while swapping weapons) |
| **Behavior Trees** | Complex AI with priorities | Boss enemy with multiple attack patterns, conditional reactions, and phase transitions |

### 6.2 Game Flow State Machine

The top-level game flow should be a state machine:

```
┌─────────┐     ┌───────────────┐     ┌──────────────┐
│  Boot   │ ──> │  Title Screen │ ──> │  Character   │
│  Load   │     │  (Main Menu)  │     │  Selection   │
└─────────┘     └───────────────┘     └──────────────┘
                       │                      │
                       ▼                      ▼
                ┌──────────────┐     ┌──────────────┐
                │   Settings   │     │   Character  │
                │              │     │   Creation   │
                └──────────────┘     └──────────────┘
                                          │
                                          ▼
                                   ┌──────────────┐
                              ┌──> │  Hub World   │ <──┐
                              │    └──────────────┘    │
                              │           │            │
                              │           ▼            │
                              │    ┌──────────────┐    │
                              │    │   Loading    │    │
                              │    │   Screen     │    │
                              │    └──────────────┘    │
                              │           │            │
                              │           ▼            │
                              │    ┌──────────────┐    │
                              │    │   Dungeon    │ ───┘
                              │    │   (In Portal)│
                              │    └──────────────┘
                              │           │
                              │           ▼
                              │    ┌──────────────┐
                              └─── │   Death /    │
                                   │   Results    │
                                   └──────────────┘
```

### 6.3 Scene Transition Architecture

For smooth transitions in Godot:

```gdscript
# scene_manager.gd (Autoload)
extends Node

signal scene_changed(scene_name: String)

var _loading_screen: PackedScene = preload("res://scenes/ui/loading_screen.tscn")
var _current_scene_name: String = ""

func transition_to(scene_path: String, transition_type: String = "fade") -> void:
    # 1. Show loading screen / transition animation
    var loading := _loading_screen.instantiate()
    get_tree().root.add_child(loading)

    # 2. Fade out
    await loading.fade_in()

    # 3. Free current scene
    get_tree().current_scene.queue_free()

    # 4. Load new scene (use ResourceLoader for async on large scenes)
    ResourceLoader.load_threaded_request(scene_path)
    while ResourceLoader.load_threaded_get_status(scene_path) != ResourceLoader.THREAD_LOAD_LOADED:
        await get_tree().process_frame

    var new_scene: PackedScene = ResourceLoader.load_threaded_get(scene_path)
    var instance := new_scene.instantiate()
    get_tree().root.add_child(instance)
    get_tree().current_scene = instance

    # 5. Fade in
    await loading.fade_out()
    loading.queue_free()

    _current_scene_name = scene_path
    scene_changed.emit(scene_path)
```

### 6.4 Loading Screen Architecture

For mobile, loading times must be masked:

1. **Async resource loading** — Use `ResourceLoader.load_threaded_request()` to avoid blocking the main thread
2. **Progress reporting** — Poll `ResourceLoader.load_threaded_get_status()` each frame for progress bars
3. **Minimum display time** — Show loading screen for at least 0.5s to avoid flashing
4. **Gameplay tips** — Display random tips, lore, or artwork during loads
5. **Procedural generation during load** — Generate dungeon layout while showing the loading screen

---

## 7. Cross-Platform Architecture

### 7.1 Platform Matrix

| Platform | Renderer | Input | Performance Target | Distribution |
|----------|----------|-------|-------------------|--------------|
| iOS (primary) | Mobile (Vulkan) | Touch, virtual joystick | 60fps, <16.6ms/frame | SwiftGodotKit embedded in native app |
| PC/Mac | Forward+ | Keyboard + Mouse, Gamepad | 60fps | Standalone Godot export |
| Console (future) | Forward+ | Gamepad | 60fps, platform TCR compliance | W4 Consoles or community ports |
| Android (future) | Mobile (Vulkan) | Touch | 60fps | Standard Godot export |

### 7.2 SwiftGodotKit Architecture (iOS)

OASIS's primary platform uses SwiftGodotKit to embed Godot inside a native iOS app.

**Architecture:**
```
┌──────────────────────────────────────┐
│  iOS App (Swift + SwiftUI)           │
│  ├── Native UI overlays             │
│  ├── Authentication (Sign in w/ Apple)│
│  ├── Push notifications             │
│  ├── In-app purchases               │
│  └── HealthKit/GameCenter integration│
│                                      │
│  ┌────────────────────────────────┐  │
│  │   SwiftGodotKit Bridge        │  │
│  │   Swift ↔ GDScript messaging  │  │
│  └────────────────────────────────┘  │
│                                      │
│  ┌────────────────────────────────┐  │
│  │   Godot Engine 4.4             │  │
│  │   (Game content, all gameplay) │  │
│  └────────────────────────────────┘  │
└──────────────────────────────────────┘
```

**Key principles for SwiftGodotKit:**
1. **Game logic lives in Godot** — All gameplay, rendering, audio, and game state management is in GDScript.
2. **Platform services live in Swift** — Authentication, payments, notifications, and native UI overlays are in Swift.
3. **Bridge is thin** — The SwiftGodotKit bridge passes simple messages and data (strings, numbers, dictionaries), not complex objects.
4. **Pin to known-good versions** — SwiftGodotKit is evolving. Pin your dependency to a specific commit, not a branch.

### 7.3 Abstraction Layer for Platform Differences

Create a platform abstraction layer so game code doesn't know which platform it's running on:

```gdscript
# platform_services.gd (Autoload)
extends Node

signal purchase_completed(product_id: String, success: bool)
signal auth_completed(user_id: String, success: bool)

func get_platform() -> String:
    if OS.has_feature("ios"):
        return "ios"
    elif OS.has_feature("macos"):
        return "macos"
    elif OS.has_feature("windows"):
        return "windows"
    elif OS.has_feature("android"):
        return "android"
    return "unknown"

func is_mobile() -> bool:
    return get_platform() in ["ios", "android"]

func is_touch_device() -> bool:
    return is_mobile()

func request_purchase(product_id: String) -> void:
    # On iOS: Bridge to SwiftUI's StoreKit
    # On PC: No-op or Steam integration
    # On Android: Google Play Billing
    pass

func save_to_cloud(data: Dictionary) -> void:
    # On iOS: iCloud via Swift bridge
    # On PC: Steam Cloud or local
    # Future: Custom cloud sync
    pass
```

### 7.4 Input Abstraction

Design input to work across all platforms from day one:

```gdscript
# input_config.gd
# Define actions in project.godot's Input Map, then query actions, never raw inputs

func _physics_process(delta: float) -> void:
    # GOOD — works on all platforms
    var direction := Input.get_vector("move_left", "move_right", "move_up", "move_down")

    # BAD — only works with specific input devices
    # var direction := Vector2(Input.get_joy_axis(0, JOY_AXIS_LEFT_X), ...)
```

**Touch controls** should be implemented as a UI overlay that emits the same input actions as physical controllers:

```gdscript
# virtual_joystick.gd
# Emits "move_left", "move_right", "move_up", "move_down" actions
# On non-touch platforms, this UI element is hidden but the same actions
# come from keyboard/gamepad
```

### 7.5 Screen Scaling Strategy

OASIS is portrait-mode mobile first. Handle scaling for different targets:

```
# project.godot settings for mobile-first
[display]
window/size/viewport_width=1170      # iPhone 15 Pro logical width
window/size/viewport_height=2532     # iPhone 15 Pro logical height
window/stretch/mode="viewport"       # Pixel-perfect scaling
window/handheld/orientation="portrait"
```

For PC/Mac (landscape), the game would need a different viewport configuration or a responsive layout system. Consider:
- Portrait mode on mobile, landscape on desktop
- Dynamic UI layout based on aspect ratio
- Safe area margins for notches/status bars on mobile

### 7.6 Console Preparation

Even if console is years away, these decisions now prevent painful rewrites later:

1. **No filesystem assumptions** — Always use `user://` paths, never absolute paths
2. **No platform-specific APIs** — Always go through the abstraction layer
3. **Respect memory budgets** — Console certification requires no memory leaks over multi-hour sessions
4. **Support gamepad** — Ensure all UI is navigable with a controller (D-pad + face buttons)
5. **60fps as a floor** — Console certification often requires a minimum framerate
6. **Text size minimums** — Console TCRs require minimum font sizes for TV readability

---

## 8. Performance Architecture

### 8.1 Frame Budget

At 60fps, you have 16.6ms per frame. Budget allocation:

```
Total: 16.6ms
  ├── Physics:     3-4ms   (CharacterBody2D, Area2D, raycasts)
  ├── Game Logic:  3-4ms   (AI, abilities, state machines, status effects)
  ├── Rendering:   6-8ms   (draw calls, shaders, particles)
  ├── Audio:       1ms     (sound playback, mixing)
  └── Overhead:    1-2ms   (signals, GDScript interpretation, OS events)
```

**Mobile-specific constraints:**
- Thermal throttling reduces available performance over time
- Battery drain scales with CPU/GPU usage
- Memory is limited (2-4GB shared between all apps on older devices)

### 8.2 Object Pooling

For projectiles, damage numbers, particles, and frequently spawned/despawned entities:

```gdscript
# object_pool.gd
class_name ObjectPool
extends Node

@export var pooled_scene: PackedScene
@export var pool_size: int = 50
@export var can_grow: bool = true

var _pool: Array[Node] = []
var _active_count: int = 0

func _ready() -> void:
    for i in pool_size:
        var obj := pooled_scene.instantiate()
        obj.visible = false
        obj.set_process(false)
        obj.set_physics_process(false)
        add_child(obj)
        _pool.append(obj)

func acquire() -> Node:
    for obj in _pool:
        if not obj.visible:
            obj.visible = true
            obj.set_process(true)
            obj.set_physics_process(true)
            _active_count += 1
            return obj
    # Pool exhausted
    if can_grow:
        var obj := pooled_scene.instantiate()
        add_child(obj)
        _pool.append(obj)
        obj.visible = true
        obj.set_process(true)
        obj.set_physics_process(true)
        _active_count += 1
        return obj
    return null  # Pool full, cannot grow

func release(obj: Node) -> void:
    obj.visible = false
    obj.set_process(false)
    obj.set_physics_process(false)
    _active_count -= 1
```

**What to pool in OASIS:**
- Enemy instances (already have `EnemyPool` autoload)
- Spell projectiles
- Damage numbers (UI labels)
- Loot drop particles
- Hit/impact VFX

**What NOT to pool:**
- Boss enemies (unique, infrequent)
- NPCs (persist in scene)
- UI panels (infrequent creation)
- Tilemap tiles (Godot handles these efficiently)

### 8.3 Rendering Performance

#### 2D Batching

Godot batches draw calls automatically when sprites share the same texture and material. To maximize batching:

1. **Use texture atlases** — Pack sprites into atlas textures. Same-atlas sprites batch into one draw call.
2. **Group by Z-index** — Batches break at Z-index boundaries. Minimize Z-index variety.
3. **Group by material** — Custom shaders break batches. Use the same material for similar objects.
4. **Use TileMapLayer** — Godot 4.3+ TileMapLayer nodes batch tiles efficiently with configurable quadrant sizes.

#### MultiMesh for Bulk Rendering

For thousands of similar sprites (grass, decorations, particles):

```gdscript
# Use MultiMeshInstance2D for bulk rendering
var multi_mesh := MultiMesh.new()
multi_mesh.mesh = your_quad_mesh
multi_mesh.transform_format = MultiMesh.TRANSFORM_2D
multi_mesh.instance_count = 1000

for i in multi_mesh.instance_count:
    multi_mesh.set_instance_transform_2d(i, Transform2D(0, position))
```

#### RenderingServer for Maximum Performance

For tens of thousands of simple visual elements (particle-like effects, bullet patterns):

```gdscript
# Direct RenderingServer usage bypasses the scene tree entirely
var canvas_item := RenderingServer.canvas_item_create()
RenderingServer.canvas_item_set_parent(canvas_item, get_canvas_item())
RenderingServer.canvas_item_add_texture_rect(canvas_item, rect, texture)
```

This is the nuclear option — use only when profiling proves scene-tree overhead is the bottleneck.

### 8.4 Culling and Visibility

Godot automatically performs frustum culling (off-screen objects aren't rendered). Additional strategies:

1. **VisibilityNotifier2D / VisibleOnScreenNotifier2D** — Disable processing for off-screen entities:
   ```gdscript
   func _on_visible_on_screen_notifier_2d_screen_exited() -> void:
       set_physics_process(false)
       # Enemy stops updating AI when off-screen

   func _on_visible_on_screen_notifier_2d_screen_entered() -> void:
       set_physics_process(true)
   ```

2. **Distance-based LOD** — Simplify distant entities (reduce animation frames, disable particles)

3. **Room-based culling** — In dungeon crawlers, only process entities in the current room and adjacent rooms

### 8.5 Physics Optimization

1. **Use simpler collision shapes** — `RectangleShape2D` and `CircleShape2D` are cheapest. Avoid `ConvexPolygonShape2D` when possible.
2. **Physics layers** — Separate layers for player, enemies, projectiles, items. Entities only check relevant layers.
3. **Disable physics for static objects** — Use `StaticBody2D` for walls, not `CharacterBody2D`.
4. **Reduce physics tick rate** if gameplay allows — 30 physics FPS with interpolation looks smooth while halving physics cost.

### 8.6 Memory Management

1. **Preload vs. Load** — `preload()` loads at script compile time (startup cost). `load()` loads at call time (runtime cost). Use preload for always-needed resources; load for conditionally-needed ones.
2. **Free unused scenes** — `queue_free()` after scene transitions. Don't keep dungeon scenes in memory while in the hub world.
3. **Resource sharing** — Godot shares `Resource` instances automatically when loaded from the same path. Don't duplicate unnecessarily.
4. **Texture compression** — Use ETC2 for mobile (default in Godot for Android/iOS). Generates smaller textures with slight quality loss.

### 8.7 GDScript Performance Tips

1. **Cache node references** — `@onready var` over repeated `get_node()` calls
2. **Avoid string operations in hot loops** — String concatenation creates garbage
3. **Use typed arrays** — `Array[Vector2]` is faster than untyped `Array`
4. **Minimize signal connections in tight loops** — Direct function calls are faster than signal emission when you know the receiver
5. **Profile before optimizing** — Use Godot's built-in Profiler and Monitors. Never guess where the bottleneck is.

### 8.8 When to Use GDExtension (C++/Rust)

Move to GDExtension when:
- **Procedural generation** is too slow in GDScript (complex pathfinding, large WFC grids)
- **Bulk processing** of thousands of entities per frame (ECS-style systems)
- **Cryptographic operations** for save integrity or networking
- **Complex math** (noise functions, spatial algorithms, physics simulations)

The godot-rust (`gdext`) bindings provide Rust's safety guarantees with near-C++ performance:
- Memory safety without garbage collection
- Zero-cost abstractions
- Excellent FFI performance
- Compile-time error catching

Architecture for GDExtension integration:
```
GDScript (game logic, UI, scene management)
  ↕ GDExtension API
Rust/C++ (procedural generation, bulk processing, pathfinding)
```

Keep the boundary clean: GDExtension handles computation-heavy tasks and exposes results as Godot-native types (Resources, Arrays, Dictionaries).

---

## 9. Plugin and Mod Architecture

### 9.1 Design Principles for Extensibility

OASIS's portal system is inherently a modding/plugin architecture — developers create Godot projects that become portals. Design for this from the start.

#### Separation of Core and Content

```
oasis-core/                     # Never modified by mods
  ├── systems/                  # Combat, inventory, stats engines
  ├── components/               # Reusable node components
  └── interfaces/               # Contracts that content must implement

oasis-content/                  # Replaceable/extensible by mods
  ├── enemies/                  # Enemy definitions (Resources)
  ├── items/                    # Item definitions (Resources)
  ├── abilities/                # Ability definitions (Resources)
  ├── dungeons/                 # Dungeon themes and configs
  └── tilesets/                 # Visual themes
```

#### The Portal API Contract

Every portal (game module) must implement a minimal interface:

```gdscript
# portal_interface.gd
class_name PortalInterface
extends Node

# Required: Called when player enters the portal
func on_enter(player_data: Dictionary) -> void:
    push_error("on_enter not implemented")

# Required: Called when player exits (return to hub)
func on_exit() -> Dictionary:
    push_error("on_exit not implemented")
    return {}

# Required: Portal metadata for hub display
func get_portal_info() -> Dictionary:
    return {
        "name": "Unnamed Portal",
        "description": "",
        "difficulty": 1,
        "min_level": 1,
        "max_players": 1,
        "creator": "",
        "thumbnail": null,
    }
```

### 9.2 Data-Only Modding (Phase 1)

The safest and simplest modding approach — mods provide data files that the engine reads:

```
mod_pack/
  ├── manifest.json             # Mod metadata, version, dependencies
  ├── items/                    # .tres files defining new items
  ├── enemies/                  # .tres files defining new enemy types
  ├── abilities/                # .tres files defining new abilities
  ├── loot_tables/              # .tres files defining loot tables
  └── themes/                   # Tilesets and visual themes
```

The engine's content databases merge mod data with base game data at load time. No code execution from mods.

### 9.3 Scripted Modding (Phase 2 — Future)

Allow mods to include GDScript for custom behaviors:

```gdscript
# Sandboxed script execution
# Use Godot's Expression class for safe evaluation
# Or define a Bytecode VM for modder-accessible operations
# NEVER allow arbitrary file system access from mod scripts
```

**Security considerations:**
- Mod scripts run in a sandbox with no filesystem access
- Network access is disabled for mod code
- Resource loading is restricted to the mod's own directory
- Memory allocation is capped per mod

### 9.4 Hot-Reloading for Development

During portal development, support hot-reloading:

```gdscript
# Watch for file changes in the portal directory
# Reload Resources when .tres files change
# Reload scenes when .tscn files change
# This dramatically speeds up content iteration
```

### 9.5 Version Compatibility

```gdscript
# manifest.json
{
    "mod_name": "The Crimson Depths",
    "mod_version": "1.0.0",
    "oasis_version_min": "0.2.0",
    "oasis_version_max": "0.3.0",
    "dependencies": [],
    "conflicts": [],
}
```

Use semantic versioning. MAJOR version changes in OASIS core may break mod compatibility — provide migration guides.

---

## 10. Networking Architecture

### 10.1 OASIS Multiplayer Roadmap

Per the strategic blueprint:
1. **Phase 1:** Single-player (current)
2. **Phase 2:** Async multiplayer (see other players in hub)
3. **Phase 3:** Real-time co-op in portals (2-4 players)
4. **Phase 4:** Hub world MMO-lite

### 10.2 Architecture for Future Networking

Design single-player code that won't need rewriting for multiplayer:

**Authoritative state principle:** Even in single-player, separate "truth" from "display":

```gdscript
# GOOD — State is separate from display
func apply_damage(target_id: int, amount: float) -> void:
    var target := _entity_registry.get(target_id)
    target.health -= amount
    # In single-player: immediate
    # In multiplayer: server validates, then replicates

# BAD — Display IS the state
func apply_damage(target: Node) -> void:
    target.get_node("HealthBar").value -= amount  # UI is the truth
```

**Entity registry pattern:** Assign unique IDs to all game entities. In single-player, these are local. In multiplayer, they become network IDs.

**Command pattern for actions:** Player actions as commands that can be:
- Executed locally (single-player)
- Sent to server for validation (client-server multiplayer)
- Recorded for replay systems

### 10.3 Rollback Netcode Considerations

For real-time co-op combat in OASIS portals:
- Deterministic game logic is required (seeded RNG, fixed-point math for physics)
- Input-based replay enables rollback — only inputs are transmitted
- Godot has community rollback netcode addons (Snopek's rollback netcode addon)
- Consider whether OASIS's combat benefits from rollback (action RPG) vs. lockstep (turn-based)

### 10.4 Replay System Architecture

If the game is deterministic (seeded dungeon generation + recorded inputs), replay is almost free:

```gdscript
# replay_data.gd
class_name ReplayData
extends Resource

@export var dungeon_seed: int
@export var dungeon_config: Dictionary
@export var player_build: Dictionary
@export var input_frames: Array[Dictionary]  # [{frame: 0, actions: ["move_right", "attack"]}, ...]
@export var rng_seeds: Dictionary  # Per-system seeds

# To replay: regenerate dungeon from seed, replay inputs frame by frame
# The entire dungeon + combat replays from a few KB of data
```

---

## 11. OASIS-Specific Recommendations

### 11.1 Recommended Architecture Refactoring

Based on the current codebase analysis:

#### Immediate (Phase 1 Polish)

1. **Introduce an Event Bus autoload** — Decouple systems that currently communicate through direct references. Start with combat events (damage dealt, enemy killed, player died) and progression events (XP gained, level up, quest complete).

2. **Convert database autoloads to Custom Resources** — `ItemDatabase`, `AbilityDatabase`, `ClassDatabase`, `RaceDatabase`, and `StatusEffectDatabase` should be Resource-based, not script autoloads with hardcoded dictionaries. This enables:
   - Editor-based content creation
   - Version-controlled `.tres` files
   - Future modding support
   - Easier testing (swap in test databases)

3. **Formalize the component pattern** — The existing components (HealthComponent, HitboxComponent, etc.) are a strong foundation. Add `class_name` declarations, consistent signal patterns, and a component documentation comment format.

4. **Add state machines to player and enemies** — Replace conditional chains in player.gd and enemy scripts with proper State nodes. This makes adding new behaviors (new abilities, new enemy phases) safe and modular.

#### Short-Term (Phase 2)

5. **Build the procedural generation pipeline** — Implement the LayoutGenerator abstraction with BSP as the first algorithm. Make the DungeonData model the shared contract between generation, population, and rendering.

6. **Implement the save schema migration chain** — The current v1→v2 migration is a great start. Formalize this with the `SaveSchema` pattern described above so every future change has a clean migration path.

7. **Create a platform abstraction layer** — Even though iOS is the only target now, wrapping platform-specific calls (save paths, input modes, screen metrics) prevents pain when PC export arrives.

#### Medium-Term (Phase 3-4)

8. **Design the portal loading architecture** — PCK hot-loading on iOS is a known risk. Research and prototype this early. The portal system's viability depends on being able to load external game content at runtime.

9. **Implement entity IDs** — Assign unique integer IDs to all game entities. This is required for save/load (reference entities by ID, not node path), networking (replicate by ID), and replay systems.

10. **Move procedural generation to GDExtension** — Once the generation pipeline is proven in GDScript, port the computationally intensive parts (BSP subdivision, WFC constraint solving, pathfinding validation) to Rust via gdext for 10-100x speedup.

### 11.2 Recommended Project Structure

```
godot-game/
  ├── project.godot
  ├── assets/
  │   ├── sprites/              # All sprite sheets and textures
  │   ├── audio/                # SFX and music
  │   ├── fonts/                # UI fonts
  │   ├── shaders/              # Visual effects shaders
  │   └── tilesets/             # Tilemap tilesets
  ├── data/                     # NEW: All game data as Resources
  │   ├── items/                # .tres files per item
  │   ├── abilities/            # .tres files per ability
  │   ├── enemies/              # .tres files per enemy type
  │   ├── classes/              # .tres files per class
  │   ├── races/                # .tres files per race
  │   ├── status_effects/       # .tres files per status effect
  │   ├── loot_tables/          # .tres files per loot table
  │   └── dungeon_configs/      # .tres files per dungeon theme
  ├── scenes/
  │   ├── player/               # Player scene + variants
  │   ├── enemies/              # Enemy scene + variants
  │   ├── dungeons/             # Dungeon scenes
  │   ├── hub/                  # Hub world scenes
  │   ├── npcs/                 # NPC scenes
  │   ├── items/                # Loot drop, chest scenes
  │   ├── portal/               # Portal scene
  │   └── ui/                   # All UI scenes
  ├── scripts/
  │   ├── components/           # Reusable behavior components
  │   ├── systems/              # Autoload systems and managers
  │   ├── states/               # State machine states
  │   │   ├── player/           # Player states (idle, run, attack, dodge, etc.)
  │   │   ├── enemy/            # Enemy states (idle, chase, attack, flee, etc.)
  │   │   └── game/             # Game flow states (menu, hub, dungeon, etc.)
  │   ├── generation/           # Procedural generation pipeline
  │   │   ├── layout/           # Layout generators (BSP, random walk, WFC)
  │   │   ├── population/       # Content populators
  │   │   └── validation/       # Dungeon validation
  │   ├── data/                 # Resource class definitions
  │   │   ├── item_data.gd
  │   │   ├── ability_data.gd
  │   │   ├── enemy_data.gd
  │   │   └── ...
  │   ├── ui/                   # UI scripts
  │   └── util/                 # Utility scripts
  └── addons/                   # Third-party plugins
```

### 11.3 Performance Budget for OASIS on iOS

Target: iPhone 12 and newer, 60fps, portrait mode

| System | Budget | Entities | Strategy |
|--------|--------|----------|----------|
| Enemies | 30 active max | Pooled, off-screen culling | EnemyPool with visibility checks |
| Projectiles | 50 active max | Pooled | Projectile pool with auto-release |
| Damage numbers | 20 active max | Pooled UI | Fade-and-release to pool |
| Particles | 200 particles max | Godot GPU particles | Use GPUParticles2D, not CPUParticles2D |
| Tilemap | Full dungeon floor | Godot internal batching | Quadrant size tuning |
| UI elements | Minimal overdraw | Godot Control nodes | Hide off-screen panels, not just invisible |

### 11.4 Technology Decision Matrix

| Decision | Choice | Rationale |
|----------|--------|-----------|
| Primary scripting | GDScript | Fastest iteration, native Godot integration, no compile step |
| Performance-critical code | GDExtension (Rust) | When profiling proves GDScript is the bottleneck; use gdext bindings |
| Data format | Custom Resources (.tres) | Type-safe, editor integration, lightweight |
| Save format | FileAccess.store_var() | Simple, supports Godot types, binary (fast), safe deserialization |
| Settings format | ConfigFile (.cfg) | Human-readable for debugging, simple key-value |
| Networking (future) | Godot high-level multiplayer + ENetMultiplayerPeer | Built-in, well-documented, sufficient for 2-4 player co-op |
| Procedural gen | GDScript → Rust migration | Prototype in GDScript, migrate hot paths to Rust when perf matters |
| State machines | Node-based FSM | Godot-native, visible in editor, debuggable |
| Content databases | Resource-based with autoload index | Editable in Godot editor, version-controlled, mergeable |

---

## Appendix A: Architecture Decision Records (ADR) Template

For every significant architecture decision, create an ADR:

```markdown
# ADR-001: [Decision Title]

**Status:** Proposed | Accepted | Deprecated | Superseded
**Date:** YYYY-MM-DD
**Context:** What problem are we solving? What constraints exist?
**Decision:** What did we decide?
**Rationale:** Why this choice over alternatives?
**Alternatives Considered:** What else did we evaluate?
**Consequences:** What are the tradeoffs? What technical debt does this create?
```

## Appendix B: Performance Profiling Checklist

Before any optimization work:

1. [ ] Profile with Godot's built-in Profiler (Debugger → Profiler tab)
2. [ ] Check Monitors for frame time breakdown (physics, process, render)
3. [ ] Identify THE bottleneck (not a bottleneck, THE bottleneck)
4. [ ] Measure before and after (commit profiling numbers to the ADR)
5. [ ] Test on target hardware (iPhone, not just desktop)
6. [ ] Test under load (spawn max entities, not just 3 test enemies)

## Appendix C: Key References

- [Game Programming Patterns](https://gameprogrammingpatterns.com/) — Robert Nystrom's canonical reference
- [Godot Best Practices](https://docs.godotengine.org/en/stable/tutorials/best_practices/index.html) — Official Godot documentation
- [GDQuest Design Patterns](https://www.gdquest.com/tutorial/godot/design-patterns/intro-to-design-patterns/) — Practical Godot pattern tutorials
- [Chickensoft Game Architecture](https://chickensoft.games/blog/game-architecture) — Three-layer architecture for Godot
- [Why Godot Isn't ECS](https://godotengine.org/article/why-isnt-godot-ecs-based-game-engine/) — Juan Linietsky's architectural rationale
- [Rooms and Mazes Dungeon Generator](https://journal.stuffwithstuff.com/2014/12/21/rooms-and-mazes/) — Bob Nystrom's dungeon generation algorithm
- [GDQuest Save Systems](https://www.gdquest.com/library/cheatsheet_save_systems/) — Complete Godot 4 save system cheat sheet
- [SwiftGodot](https://github.com/migueldeicaza/SwiftGodot) — Swift bindings for Godot 4
- [SwiftGodotKit](https://github.com/migueldeicaza/SwiftGodotKit) — Embed Godot in Swift apps
- [godot-rust (gdext)](https://github.com/godot-rust/gdext) — Rust bindings for Godot 4
- [W4 Consoles](https://www.w4games.com/w4consoles) — Console export support for Godot
- [Godot Rollback Netcode](https://gitlab.com/snopek-games/godot-rollback-netcode) — Rollback netcode addon
- [RogueBasin Save Files](https://www.roguebasin.com/index.php?title=Save_Files) — Roguelike save system wisdom
- [Godot Mobile Optimization](https://docs.godotengine.org/en/stable/tutorials/performance/general_optimization.html) — Official performance guide
- [Godot LOD System](https://docs.godotengine.org/cs/4.x/tutorials/3d/mesh_lod.html) — Mesh level of detail documentation

---

*This document is a living reference. Update it as OASIS architecture evolves and new patterns are discovered.*
