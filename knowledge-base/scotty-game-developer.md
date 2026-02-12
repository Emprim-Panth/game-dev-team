# Lead Game Developer: Godot 4.x & GDScript Expertise

> Scotty's domain expertise for Forge&Code indie game studio.
> Covers Godot 4.4+ architecture, GDScript patterns, procedural generation,
> mobile optimization, and iOS deployment.

---

## Table of Contents

1. [Godot 4 Architecture & Node System](#1-godot-4-architecture--node-system)
2. [GDScript Patterns & Style Guide](#2-gdscript-patterns--style-guide)
3. [GDScript Anti-Patterns](#3-gdscript-anti-patterns)
4. [Procedural Dungeon Generation Algorithms](#4-procedural-dungeon-generation-algorithms)
5. [Tilemap System & Navigation](#5-tilemap-system--navigation)
6. [Game Programming Patterns in GDScript](#6-game-programming-patterns-in-gdscript)
7. [Mobile Optimization Techniques](#7-mobile-optimization-techniques)
8. [iOS-Specific Considerations](#8-ios-specific-considerations)
9. [Roguelike Project Structure Template](#9-roguelike-project-structure-template)
10. [Godot 4.4+ Feature Reference](#10-godot-44-feature-reference)
11. [Performance Profiling & Measurement](#11-performance-profiling--measurement)
12. [Resource System & Data Management](#12-resource-system--data-management)

---

## 1. Godot 4 Architecture & Node System

### Core Concepts

Godot's architecture is built on three pillars: **Nodes**, **Scenes**, and the **Scene Tree**.

- **Node**: The atomic building block. Each node type inherits from its parent class (e.g., `Sprite2D -> Node2D -> CanvasItem -> Node`).
- **Scene**: A reusable tree of nodes saved as `.tscn` files. Scenes ARE the reusable components.
- **Scene Tree**: The runtime hierarchy. All active nodes live here. Parent-child relationships determine transform inheritance, render order, and lifecycle.

### Composition Over Inheritance

Godot actively promotes **composition over inheritance** to avoid fragile class chains.

**Composition approach** (preferred):
```
Player (CharacterBody2D)
  +-- Sprite2D           # Visual
  +-- CollisionShape2D   # Physics
  +-- AnimationPlayer     # Animation
  +-- StateMachine        # Behavior
  +-- HitboxComponent     # Damage dealing
  +-- HurtboxComponent    # Damage receiving
  +-- HealthComponent     # Health tracking
```

**When to use inheritance**:
- Shared behavior across similar entities (e.g., `BaseEnemy` -> `Slime`, `Skeleton`)
- Virtual method overrides where polymorphism is needed
- Script inheritance with `extends` for code reuse

**When to use composition**:
- Mixing unrelated behaviors (health + movement + inventory)
- Rapid prototyping and flexible entity building
- When you need to add/remove capabilities at runtime

### Scene-Driven Design

Structure your game so scenes mirror what players see:

```
# Good: Each game element is a self-contained scene
res://scenes/entities/player.tscn
res://scenes/entities/enemies/slime.tscn
res://scenes/ui/hud.tscn
res://scenes/levels/dungeon_floor.tscn

# Bad: Monolithic scenes with everything
res://main_game.tscn  # Contains player, enemies, UI, level...
```

### When NOT to Use Nodes

Not everything needs to be a Node. Use plain classes/resources for:
- Pure data containers (item definitions, stat blocks)
- Algorithms (pathfinding helpers, RNG utilities)
- State machines that don't need scene tree access
- Configuration objects

```gdscript
# Plain class - no node overhead
class_name DungeonRoom

var rect: Rect2i
var connections: Array[DungeonRoom] = []
var room_type: RoomType = RoomType.NORMAL

enum RoomType { NORMAL, TREASURE, BOSS, SHOP, SECRET }

func get_center() -> Vector2i:
    return rect.position + rect.size / 2
```

### Autoloads (Singletons)

Autoloads persist across scene changes. Use sparingly:

**Good uses:**
- Event bus (global signal hub)
- Audio manager (prevents audio cut on scene change)
- Game state (persistent player data, run progress)
- Save/load system

**Bad uses:**
- Level-specific logic
- Entity management (use a node in the level scene instead)
- Anything that should reset between scenes

```gdscript
# Project Settings -> Autoload -> Add these:
# Events.gd   -> "Events"
# GameState.gd -> "GameState"
# AudioManager.gd -> "AudioManager"
```

---

## 2. GDScript Patterns & Style Guide

### File Structure Order (Mandatory)

Every GDScript file follows this exact order:

```gdscript
@tool                          # 1. Annotations (@tool, @icon, @static_unload)
@icon("res://icon.svg")
class_name MyClass             # 2. class_name
extends Node2D                 # 3. extends

## Brief description of the class.
##
## Longer description with details about what this class does,
## how it should be used, and any important notes.   # 4. Doc comment

# --- Signals --- #
signal health_changed(new_health: int)               # 5. Signals (past tense)
signal died
signal attack_finished

# --- Enums --- #
enum State { IDLE, RUNNING, JUMPING, FALLING }       # 6. Enums

# --- Constants --- #
const MAX_SPEED := 300.0                             # 7. Constants
const GRAVITY := 980.0

# --- Static Variables --- #
static var instance_count: int = 0                   # 8. Static variables

# --- Exported Variables --- #
@export var speed: float = 200.0                     # 9. @export variables
@export var jump_force: float = 400.0
@export_group("Combat")
@export var max_health: int = 100
@export var attack_damage: int = 10

# --- Public Variables --- #
var current_health: int = 100                        # 10. Public variables
var is_alive: bool = true
var velocity_component: Vector2 = Vector2.ZERO

# --- Private Variables --- #
var _state: State = State.IDLE                       # 11. Private variables
var _attack_cooldown: float = 0.0

# --- @onready Variables --- #
@onready var _sprite: Sprite2D = $Sprite2D           # 12. @onready (near _ready)
@onready var _anim: AnimationPlayer = $AnimationPlayer
@onready var _hitbox: Area2D = $Hitbox


# --- Lifecycle Methods --- #
func _ready() -> void:                               # 13. Built-in virtuals
    _sprite.modulate = Color.WHITE
    instance_count += 1


func _process(delta: float) -> void:
    _update_cooldowns(delta)


func _physics_process(delta: float) -> void:
    _apply_gravity(delta)
    move_and_slide()


func _unhandled_input(event: InputEvent) -> void:
    if event.is_action_pressed("attack"):
        attack()


# --- Public Methods --- #
func attack() -> void:                               # 14. Public methods
    if _attack_cooldown > 0.0:
        return
    _attack_cooldown = 0.5
    _anim.play("attack")


func take_damage(amount: int) -> void:
    current_health -= amount
    health_changed.emit(current_health)
    if current_health <= 0:
        _die()


# --- Private Methods --- #
func _die() -> void:                                 # 15. Private methods
    is_alive = false
    died.emit()
    queue_free()


func _apply_gravity(delta: float) -> void:
    velocity.y += GRAVITY * delta


func _update_cooldowns(delta: float) -> void:
    _attack_cooldown = maxf(0.0, _attack_cooldown - delta)
```

### Naming Conventions

| Element | Convention | Example |
|---------|-----------|---------|
| Files & folders | `snake_case` | `player_controller.gd` |
| Classes | `PascalCase` | `class_name PlayerController` |
| Functions | `snake_case` | `func take_damage()` |
| Variables | `snake_case` | `var max_health` |
| Private members | `_snake_case` | `var _internal_state` |
| Constants | `UPPER_SNAKE_CASE` | `const MAX_SPEED` |
| Enums (type) | `PascalCase` | `enum Direction` |
| Enums (values) | `UPPER_SNAKE_CASE` | `Direction.UP_LEFT` |
| Signals | `snake_case` past tense | `signal health_changed` |
| Booleans | `is_`, `can_`, `has_` prefix | `var is_alive: bool` |
| Signal callbacks | `_on_` prefix | `func _on_body_entered()` |

### Static Typing (Always Use It)

Static typing improves performance (optimized opcodes), catches errors at compile time, and enables autocompletion.

```gdscript
# ALWAYS type everything
var speed: float = 200.0
var direction: Vector2 = Vector2.ZERO
var enemies: Array[Enemy] = []
var inventory: Dictionary[String, int] = {}  # Godot 4.4+ typed dict

# Use := for type inference when the type is obvious
var health := 100                  # inferred as int
var position := Vector2.ZERO       # inferred as Vector2
@onready var sprite := $Sprite2D as Sprite2D

# Type function parameters and return values
func calculate_damage(base: int, multiplier: float) -> int:
    return int(base * multiplier)

# Typed arrays
var path: Array[Vector2i] = []
var room_list: Array[Rect2i] = []
```

### Guard Clauses

Use early returns to reduce nesting:

```gdscript
# Good: Guard clauses
func take_damage(amount: int) -> void:
    if not is_alive:
        return
    if amount <= 0:
        return
    if _is_invincible:
        return

    current_health -= amount
    health_changed.emit(current_health)
    if current_health <= 0:
        _die()

# Bad: Deep nesting
func take_damage(amount: int) -> void:
    if is_alive:
        if amount > 0:
            if not _is_invincible:
                current_health -= amount
                # ... deeply nested logic
```

---

## 3. GDScript Anti-Patterns

### Anti-Pattern: God Autoloads

```gdscript
# BAD: One autoload doing everything
# GameManager.gd (autoload)
extends Node

var player_health: int
var current_level: int
var inventory: Array
var quest_log: Array
var settings: Dictionary
var audio_volumes: Dictionary
# ... 500 more lines of unrelated state

# GOOD: Separate concerns into focused autoloads
# GameState.gd -> persistent run data only
# Events.gd -> signal bus only
# AudioManager.gd -> audio only
# SaveManager.gd -> save/load only
```

### Anti-Pattern: Signal Spaghetti

```gdscript
# BAD: Bubbling signals through 5 layers
# Enemy emits -> EnemyManager re-emits -> Level re-emits -> Game re-emits -> HUD connects
# Every intermediary has: signal enemy_died; func _on_enemy_died(): enemy_died.emit()

# GOOD: Use an event bus for distant communication
# Events.gd (autoload)
signal enemy_killed(enemy_type: String, position: Vector2)
signal floor_cleared
signal gold_collected(amount: int)

# Enemy.gd - emits directly to bus
func _die() -> void:
    Events.enemy_killed.emit(enemy_type, global_position)
    queue_free()

# HUD.gd - connects directly from bus
func _ready() -> void:
    Events.enemy_killed.connect(_on_enemy_killed)
```

### Anti-Pattern: Heavy _process()

```gdscript
# BAD: Doing expensive work every frame
func _process(delta: float) -> void:
    var nearest_enemy := _find_nearest_enemy()  # O(n) every frame
    var path := _calculate_path(nearest_enemy)  # pathfinding every frame
    _update_minimap()                           # full redraw every frame

# GOOD: Use timers, signals, and dirty flags
var _path_dirty := true
var _cached_path: Array[Vector2] = []

func _ready() -> void:
    # Recalculate path periodically, not every frame
    var timer := Timer.new()
    timer.wait_time = 0.25
    timer.timeout.connect(_recalculate_path)
    add_child(timer)
    timer.start()

func _process(delta: float) -> void:
    if _cached_path.size() > 0:
        _follow_path(delta)

func _recalculate_path() -> void:
    if _path_dirty:
        _cached_path = _navigation.get_path(global_position, _target_position)
        _path_dirty = false
```

### Anti-Pattern: String-Based Lookups

```gdscript
# BAD: String comparisons and magic strings
if enemy.type == "skeleton":
    damage *= 2
if weapon.element == "fire":
    apply_burn()

# GOOD: Enums are faster and type-safe
enum EnemyType { SLIME, SKELETON, BOSS }
enum Element { NONE, FIRE, ICE, LIGHTNING }

if enemy.type == EnemyType.SKELETON:
    damage *= 2
if weapon.element == Element.FIRE:
    apply_burn()
```

### Anti-Pattern: Creating Nodes in _process()

```gdscript
# BAD: Instantiating every frame
func _process(delta: float) -> void:
    if is_shooting:
        var bullet := bullet_scene.instantiate()
        add_child(bullet)  # Creates new node every frame

# GOOD: Object pooling (see Section 6)
func _process(delta: float) -> void:
    if is_shooting and _shoot_timer <= 0.0:
        var bullet := BulletPool.get_bullet()
        bullet.activate(global_position, _aim_direction)
        _shoot_timer = shoot_cooldown
```

### Anti-Pattern: Not Using Typed Arrays

```gdscript
# BAD: Untyped, no autocompletion, no compile-time checks
var enemies = []
var items = {}

# GOOD: Typed, safe, slightly faster
var enemies: Array[Enemy] = []
var items: Dictionary[String, ItemData] = {}  # 4.4+
```

---

## 4. Procedural Dungeon Generation Algorithms

### 4.1 Binary Space Partitioning (BSP)

BSP creates structured, non-overlapping room layouts. Best for traditional dungeon layouts.

```gdscript
class_name BSPDungeon
extends RefCounted

## BSP tree node representing a partition of space.
class BSPNode:
    var rect: Rect2i
    var left: BSPNode = null
    var right: BSPNode = null
    var room: Rect2i = Rect2i()

    func _init(r: Rect2i) -> void:
        rect = r

    func is_leaf() -> bool:
        return left == null and right == null


const MIN_PARTITION_SIZE := 10  # Minimum width/height for a partition
const MIN_ROOM_SIZE := 6       # Minimum room dimension
const ROOM_PADDING := 2        # Padding between room and partition edge
const SPLIT_RATIO_MIN := 0.35  # Prevents extremely thin partitions
const SPLIT_RATIO_MAX := 0.65

var _rng := RandomNumberGenerator.new()
var _width: int
var _height: int
var _grid: Array[Array]  # 2D grid: 0 = floor, 1 = wall
var _rooms: Array[Rect2i] = []


func generate(width: int, height: int, seed_value: int = -1) -> Array[Array]:
    _width = width
    _height = height

    if seed_value >= 0:
        _rng.seed = seed_value
    else:
        _rng.randomize()

    # Initialize grid as all walls
    _grid = []
    for y in height:
        var row: Array[int] = []
        row.resize(width)
        row.fill(1)
        _grid.append(row)

    _rooms.clear()

    # Build BSP tree
    var root := BSPNode.new(Rect2i(0, 0, width, height))
    _split_recursive(root, 5)  # 5 levels of recursion

    # Place rooms in leaf nodes
    _place_rooms(root)

    # Connect sibling rooms with corridors
    _connect_rooms(root)

    return _grid


func _split_recursive(node: BSPNode, depth: int) -> void:
    if depth <= 0:
        return
    if node.rect.size.x < MIN_PARTITION_SIZE * 2 and node.rect.size.y < MIN_PARTITION_SIZE * 2:
        return

    # Choose split direction based on aspect ratio
    var split_horizontal: bool
    if node.rect.size.x > node.rect.size.y * 1.25:
        split_horizontal = false  # Split vertically (wide rooms)
    elif node.rect.size.y > node.rect.size.x * 1.25:
        split_horizontal = true   # Split horizontally (tall rooms)
    else:
        split_horizontal = _rng.randf() > 0.5

    if split_horizontal:
        if node.rect.size.y < MIN_PARTITION_SIZE * 2:
            return
        var split_y := node.rect.position.y + int(
            node.rect.size.y * _rng.randf_range(SPLIT_RATIO_MIN, SPLIT_RATIO_MAX)
        )
        node.left = BSPNode.new(Rect2i(
            node.rect.position,
            Vector2i(node.rect.size.x, split_y - node.rect.position.y)
        ))
        node.right = BSPNode.new(Rect2i(
            Vector2i(node.rect.position.x, split_y),
            Vector2i(node.rect.size.x, node.rect.end.y - split_y)
        ))
    else:
        if node.rect.size.x < MIN_PARTITION_SIZE * 2:
            return
        var split_x := node.rect.position.x + int(
            node.rect.size.x * _rng.randf_range(SPLIT_RATIO_MIN, SPLIT_RATIO_MAX)
        )
        node.left = BSPNode.new(Rect2i(
            node.rect.position,
            Vector2i(split_x - node.rect.position.x, node.rect.size.y)
        ))
        node.right = BSPNode.new(Rect2i(
            Vector2i(split_x, node.rect.position.y),
            Vector2i(node.rect.end.x - split_x, node.rect.size.y)
        ))

    _split_recursive(node.left, depth - 1)
    _split_recursive(node.right, depth - 1)


func _place_rooms(node: BSPNode) -> void:
    if node.is_leaf():
        # Random room within partition bounds with padding
        var room_w := _rng.randi_range(MIN_ROOM_SIZE, node.rect.size.x - ROOM_PADDING * 2)
        var room_h := _rng.randi_range(MIN_ROOM_SIZE, node.rect.size.y - ROOM_PADDING * 2)
        var room_x := _rng.randi_range(
            node.rect.position.x + ROOM_PADDING,
            node.rect.end.x - room_w - ROOM_PADDING
        )
        var room_y := _rng.randi_range(
            node.rect.position.y + ROOM_PADDING,
            node.rect.end.y - room_h - ROOM_PADDING
        )

        node.room = Rect2i(room_x, room_y, room_w, room_h)
        _rooms.append(node.room)

        # Carve room into grid
        for y in range(room_y, room_y + room_h):
            for x in range(room_x, room_x + room_w):
                if x >= 0 and x < _width and y >= 0 and y < _height:
                    _grid[y][x] = 0
    else:
        if node.left:
            _place_rooms(node.left)
        if node.right:
            _place_rooms(node.right)


func _connect_rooms(node: BSPNode) -> void:
    if node.is_leaf():
        return
    if node.left == null or node.right == null:
        return

    _connect_rooms(node.left)
    _connect_rooms(node.right)

    # Find a room in each subtree and connect their centers
    var left_room := _find_room(node.left)
    var right_room := _find_room(node.right)

    if left_room.size != Vector2i.ZERO and right_room.size != Vector2i.ZERO:
        var center_a := left_room.position + left_room.size / 2
        var center_b := right_room.position + right_room.size / 2
        _carve_corridor(center_a, center_b)


func _find_room(node: BSPNode) -> Rect2i:
    if node.is_leaf():
        return node.room
    # Prefer left subtree, fallback to right
    if node.left:
        var room := _find_room(node.left)
        if room.size != Vector2i.ZERO:
            return room
    if node.right:
        return _find_room(node.right)
    return Rect2i()


func _carve_corridor(from: Vector2i, to: Vector2i) -> void:
    # L-shaped corridor: go horizontal first, then vertical
    var x := from.x
    var y := from.y

    # Horizontal segment
    var x_dir := 1 if to.x > x else -1
    while x != to.x:
        if x >= 0 and x < _width and y >= 0 and y < _height:
            _grid[y][x] = 0
            # Make corridor 2 tiles wide for better navigation
            if y + 1 < _height:
                _grid[y + 1][x] = 0
        x += x_dir

    # Vertical segment
    var y_dir := 1 if to.y > y else -1
    while y != to.y:
        if x >= 0 and x < _width and y >= 0 and y < _height:
            _grid[y][x] = 0
            if x + 1 < _width:
                _grid[y][x + 1] = 0
        y += y_dir


func get_rooms() -> Array[Rect2i]:
    return _rooms


func get_spawn_position() -> Vector2i:
    if _rooms.size() > 0:
        return _rooms[0].position + _rooms[0].size / 2
    return Vector2i.ZERO


func get_exit_position() -> Vector2i:
    if _rooms.size() > 1:
        var last := _rooms[_rooms.size() - 1]
        return last.position + last.size / 2
    return Vector2i(_width / 2, _height / 2)
```

### 4.2 Cellular Automata (Cave Generation)

Creates organic, cave-like environments. Best for natural caverns and organic shapes.

```gdscript
class_name CaveGenerator
extends RefCounted

## Generates cave-like levels using cellular automata.
## Rule: B678/S345678
## - Birth: dead cell with 6-8 living neighbors becomes alive (wall)
## - Survival: living cell with 3-8 living neighbors stays alive
## - Otherwise: cell dies (becomes floor)

const WALL := 1
const FLOOR := 0

var _rng := RandomNumberGenerator.new()
var _width: int
var _height: int


func generate(
    width: int,
    height: int,
    fill_probability: float = 0.45,
    iterations: int = 5,
    seed_value: int = -1
) -> Array[Array]:
    _width = width
    _height = height

    if seed_value >= 0:
        _rng.seed = seed_value
    else:
        _rng.randomize()

    # Step 1: Initialize random grid
    var grid := _initialize_grid(fill_probability)

    # Step 2: Run cellular automata iterations
    for i in iterations:
        grid = _step(grid)

    # Step 3: Smooth pass (B5678/S5678 - removes convex corners)
    grid = _smooth(grid)

    # Step 4: Ensure border is solid
    _seal_borders(grid)

    # Step 5: Flood fill to find and connect caverns
    grid = _ensure_connectivity(grid)

    return grid


func _initialize_grid(fill_probability: float) -> Array[Array]:
    var grid: Array[Array] = []
    for y in _height:
        var row: Array[int] = []
        for x in _width:
            # Borders are always walls
            if x == 0 or x == _width - 1 or y == 0 or y == _height - 1:
                row.append(WALL)
            else:
                row.append(WALL if _rng.randf() < fill_probability else FLOOR)
        grid.append(row)
    return grid


func _step(grid: Array[Array]) -> Array[Array]:
    var new_grid: Array[Array] = []
    for y in _height:
        var row: Array[int] = []
        for x in _width:
            var neighbors := _count_neighbors(grid, x, y)
            if grid[y][x] == WALL:
                # Survival: wall stays if 3+ neighbors (S345678)
                row.append(WALL if neighbors >= 3 else FLOOR)
            else:
                # Birth: floor becomes wall if 6+ neighbors (B678)
                row.append(WALL if neighbors >= 6 else FLOOR)
        new_grid.append(row)
    return new_grid


func _smooth(grid: Array[Array]) -> Array[Array]:
    ## Smoothing pass using B5678/S5678 to remove artifacts.
    var new_grid: Array[Array] = []
    for y in _height:
        var row: Array[int] = []
        for x in _width:
            var neighbors := _count_neighbors(grid, x, y)
            row.append(WALL if neighbors >= 5 else FLOOR)
        new_grid.append(row)
    return new_grid


func _count_neighbors(grid: Array[Array], cx: int, cy: int) -> int:
    var count := 0
    for dy in range(-1, 2):
        for dx in range(-1, 2):
            if dx == 0 and dy == 0:
                continue
            var nx := cx + dx
            var ny := cy + dy
            # Out of bounds counts as wall
            if nx < 0 or nx >= _width or ny < 0 or ny >= _height:
                count += 1
            elif grid[ny][nx] == WALL:
                count += 1
    return count


func _seal_borders(grid: Array[Array]) -> void:
    for x in _width:
        grid[0][x] = WALL
        grid[_height - 1][x] = WALL
    for y in _height:
        grid[y][0] = WALL
        grid[y][_width - 1] = WALL


func _ensure_connectivity(grid: Array[Array]) -> Array[Array]:
    ## Flood fill to find cavern regions, then connect them.
    var visited: Array[Array] = []
    for y in _height:
        var row: Array[bool] = []
        row.resize(_width)
        row.fill(false)
        visited.append(row)

    var regions: Array[Array] = []  # Array of Array[Vector2i]

    # Find all floor regions
    for y in _height:
        for x in _width:
            if grid[y][x] == FLOOR and not visited[y][x]:
                var region := _flood_fill(grid, visited, x, y)
                regions.append(region)

    if regions.size() <= 1:
        return grid

    # Sort by size descending - keep largest
    regions.sort_custom(func(a: Array, b: Array) -> bool: return a.size() > b.size())

    # Connect each smaller region to the largest
    var main_region: Array = regions[0]
    for i in range(1, regions.size()):
        var other_region: Array = regions[i]
        if other_region.size() < 10:
            # Fill tiny regions (they're just noise)
            for pos: Vector2i in other_region:
                grid[pos.y][pos.x] = WALL
        else:
            # Connect to main region via corridor
            var closest := _find_closest_pair(main_region, other_region)
            _carve_tunnel(grid, closest[0], closest[1])
            main_region.append_array(other_region)

    return grid


func _flood_fill(
    grid: Array[Array],
    visited: Array[Array],
    start_x: int,
    start_y: int
) -> Array[Vector2i]:
    var region: Array[Vector2i] = []
    var stack: Array[Vector2i] = [Vector2i(start_x, start_y)]

    while stack.size() > 0:
        var pos := stack.pop_back()
        if pos.x < 0 or pos.x >= _width or pos.y < 0 or pos.y >= _height:
            continue
        if visited[pos.y][pos.x] or grid[pos.y][pos.x] == WALL:
            continue

        visited[pos.y][pos.x] = true
        region.append(pos)

        stack.append(Vector2i(pos.x + 1, pos.y))
        stack.append(Vector2i(pos.x - 1, pos.y))
        stack.append(Vector2i(pos.x, pos.y + 1))
        stack.append(Vector2i(pos.x, pos.y - 1))

    return region


func _find_closest_pair(region_a: Array, region_b: Array) -> Array[Vector2i]:
    var best_dist := INF
    var best_a := Vector2i.ZERO
    var best_b := Vector2i.ZERO

    # Sample to avoid O(n*m) for large regions
    var sample_a := region_a.slice(0, mini(region_a.size(), 100))
    var sample_b := region_b.slice(0, mini(region_b.size(), 100))

    for a: Vector2i in sample_a:
        for b: Vector2i in sample_b:
            var dist := (a - b).length_squared()
            if dist < best_dist:
                best_dist = dist
                best_a = a
                best_b = b

    return [best_a, best_b]


func _carve_tunnel(grid: Array[Array], from: Vector2i, to: Vector2i) -> void:
    var pos := from
    while pos != to:
        if pos.x >= 0 and pos.x < _width and pos.y >= 0 and pos.y < _height:
            grid[pos.y][pos.x] = FLOOR
            # Make tunnel 2-wide
            if pos.x + 1 < _width:
                grid[pos.y][pos.x + 1] = FLOOR
            if pos.y + 1 < _height:
                grid[pos.y + 1][pos.x] = FLOOR

        # Move toward target
        if absi(to.x - pos.x) > absi(to.y - pos.y):
            pos.x += 1 if to.x > pos.x else -1
        else:
            pos.y += 1 if to.y > pos.y else -1
```

### 4.3 Room-and-Corridor (Random Placement)

Places rooms randomly, rejects overlaps, connects via minimum spanning tree.

```gdscript
class_name RoomCorridorDungeon
extends RefCounted

## Generates dungeons by randomly placing rooms and connecting them.
## Uses rejection sampling for room placement and Prim's MST for corridors.

var _rng := RandomNumberGenerator.new()
var _width: int
var _height: int
var _rooms: Array[Rect2i] = []
var _grid: Array[Array] = []


func generate(
    width: int,
    height: int,
    max_rooms: int = 15,
    min_room_size: int = 5,
    max_room_size: int = 12,
    seed_value: int = -1
) -> Array[Array]:
    _width = width
    _height = height
    _rooms.clear()

    if seed_value >= 0:
        _rng.seed = seed_value
    else:
        _rng.randomize()

    # Initialize all walls
    _grid = []
    for y in height:
        var row: Array[int] = []
        row.resize(width)
        row.fill(1)
        _grid.append(row)

    # Place rooms with rejection sampling
    var attempts := 0
    while _rooms.size() < max_rooms and attempts < max_rooms * 10:
        var w := _rng.randi_range(min_room_size, max_room_size)
        var h := _rng.randi_range(min_room_size, max_room_size)
        var x := _rng.randi_range(2, width - w - 2)
        var y := _rng.randi_range(2, height - h - 2)
        var new_room := Rect2i(x, y, w, h)

        if not _overlaps_existing(new_room):
            _rooms.append(new_room)
            _carve_room(new_room)
        attempts += 1

    # Connect rooms using MST (Prim's algorithm)
    _connect_via_mst()

    return _grid


func _overlaps_existing(room: Rect2i) -> bool:
    var padded := Rect2i(room.position - Vector2i(2, 2), room.size + Vector2i(4, 4))
    for existing in _rooms:
        if padded.intersects(existing):
            return true
    return false


func _carve_room(room: Rect2i) -> void:
    for y in range(room.position.y, room.end.y):
        for x in range(room.position.x, room.end.x):
            _grid[y][x] = 0


func _connect_via_mst() -> void:
    ## Prim's algorithm to create minimum spanning tree of rooms.
    if _rooms.size() < 2:
        return

    var connected: Array[int] = [0]
    var remaining: Array[int] = []
    for i in range(1, _rooms.size()):
        remaining.append(i)

    while remaining.size() > 0:
        var best_dist := INF
        var best_connected := 0
        var best_remaining := 0

        for ci in connected:
            var center_a := _rooms[ci].position + _rooms[ci].size / 2
            for ri in remaining:
                var center_b := _rooms[ri].position + _rooms[ri].size / 2
                var dist := (center_a - center_b).length_squared()
                if dist < best_dist:
                    best_dist = dist
                    best_connected = ci
                    best_remaining = ri

        # Carve corridor between closest pair
        var from := _rooms[best_connected].position + _rooms[best_connected].size / 2
        var to := _rooms[best_remaining].position + _rooms[best_remaining].size / 2
        _carve_l_corridor(from, to)

        connected.append(best_remaining)
        remaining.erase(best_remaining)

    # Add 1-2 extra connections for loops (more interesting for roguelikes)
    var extra := _rng.randi_range(1, mini(3, _rooms.size() / 3))
    for i in extra:
        var a := _rng.randi_range(0, _rooms.size() - 1)
        var b := _rng.randi_range(0, _rooms.size() - 1)
        if a != b:
            var from := _rooms[a].position + _rooms[a].size / 2
            var to := _rooms[b].position + _rooms[b].size / 2
            _carve_l_corridor(from, to)


func _carve_l_corridor(from: Vector2i, to: Vector2i) -> void:
    ## L-shaped corridor: randomly choose horizontal-first or vertical-first.
    if _rng.randf() > 0.5:
        _carve_horizontal(from.x, to.x, from.y)
        _carve_vertical(from.y, to.y, to.x)
    else:
        _carve_vertical(from.y, to.y, from.x)
        _carve_horizontal(from.x, to.x, to.y)


func _carve_horizontal(x1: int, x2: int, y: int) -> void:
    for x in range(mini(x1, x2), maxi(x1, x2) + 1):
        if x >= 0 and x < _width and y >= 0 and y < _height:
            _grid[y][x] = 0


func _carve_vertical(y1: int, y2: int, x: int) -> void:
    for y in range(mini(y1, y2), maxi(y1, y2) + 1):
        if x >= 0 and x < _width and y >= 0 and y < _height:
            _grid[y][x] = 0
```

### 4.4 Wave Function Collapse (WFC)

Constraint-based generation for structured, tile-compatible layouts. Best for levels that must conform to specific tile adjacency rules.

```gdscript
class_name WFCGenerator
extends RefCounted

## Wave Function Collapse for tilemap-compatible dungeon generation.
## Each cell starts with all possible tiles, then collapses based on
## adjacency constraints until every cell has exactly one tile.

class Cell:
    var possible_tiles: Array[int] = []
    var collapsed: bool = false
    var tile: int = -1

    func entropy() -> int:
        return possible_tiles.size()

    func collapse(rng: RandomNumberGenerator) -> void:
        if possible_tiles.size() == 0:
            push_error("WFC: Cell has no possible tiles - contradiction!")
            tile = 0
        else:
            tile = possible_tiles[rng.randi_range(0, possible_tiles.size() - 1)]
        possible_tiles = [tile]
        collapsed = true


# Tile IDs
enum Tile { FLOOR, WALL, CORRIDOR, DOOR, WALL_TOP, WALL_CORNER }

# Adjacency rules: for each tile, which tiles can be adjacent in each direction
# Directions: 0=up, 1=right, 2=down, 3=left
var _adjacency_rules: Dictionary = {}
var _rng := RandomNumberGenerator.new()
var _width: int
var _height: int
var _cells: Array[Array] = []  # 2D array of Cell


func setup_rules() -> void:
    ## Define which tiles can neighbor each other.
    ## Format: _adjacency_rules[tile_id] = [up_neighbors, right_neighbors, down_neighbors, left_neighbors]
    _adjacency_rules[Tile.FLOOR] = [
        [Tile.FLOOR, Tile.WALL_TOP, Tile.CORRIDOR, Tile.DOOR],  # up
        [Tile.FLOOR, Tile.WALL, Tile.CORRIDOR, Tile.DOOR],      # right
        [Tile.FLOOR, Tile.WALL, Tile.CORRIDOR, Tile.DOOR],      # down
        [Tile.FLOOR, Tile.WALL, Tile.CORRIDOR, Tile.DOOR],      # left
    ]
    _adjacency_rules[Tile.WALL] = [
        [Tile.WALL, Tile.WALL_TOP, Tile.WALL_CORNER],
        [Tile.WALL, Tile.WALL_CORNER, Tile.FLOOR, Tile.CORRIDOR],
        [Tile.WALL, Tile.WALL_TOP, Tile.WALL_CORNER],
        [Tile.WALL, Tile.WALL_CORNER, Tile.FLOOR, Tile.CORRIDOR],
    ]
    _adjacency_rules[Tile.CORRIDOR] = [
        [Tile.CORRIDOR, Tile.FLOOR, Tile.DOOR, Tile.WALL],
        [Tile.CORRIDOR, Tile.FLOOR, Tile.DOOR, Tile.WALL],
        [Tile.CORRIDOR, Tile.FLOOR, Tile.DOOR, Tile.WALL],
        [Tile.CORRIDOR, Tile.FLOOR, Tile.DOOR, Tile.WALL],
    ]
    _adjacency_rules[Tile.DOOR] = [
        [Tile.WALL, Tile.WALL_TOP],
        [Tile.FLOOR, Tile.CORRIDOR],
        [Tile.WALL, Tile.WALL_TOP],
        [Tile.FLOOR, Tile.CORRIDOR],
    ]


func generate(width: int, height: int, seed_value: int = -1) -> Array[Array]:
    _width = width
    _height = height

    if seed_value >= 0:
        _rng.seed = seed_value
    else:
        _rng.randomize()

    setup_rules()
    _initialize_cells()

    # Main WFC loop
    var max_iterations := width * height * 2  # Safety limit
    var iteration := 0

    while not _all_collapsed() and iteration < max_iterations:
        # 1. Find cell with lowest entropy (fewest possibilities)
        var cell_pos := _find_lowest_entropy()
        if cell_pos == Vector2i(-1, -1):
            break

        # 2. Collapse that cell to a random valid tile
        _cells[cell_pos.y][cell_pos.x].collapse(_rng)

        # 3. Propagate constraints to neighbors
        _propagate(cell_pos)

        iteration += 1

    # Convert to output grid
    return _build_output()


func _initialize_cells() -> void:
    var all_tiles: Array[int] = []
    for tile_id in _adjacency_rules:
        all_tiles.append(tile_id)

    _cells = []
    for y in _height:
        var row: Array[Cell] = []
        for x in _width:
            var cell := Cell.new()
            cell.possible_tiles = all_tiles.duplicate()
            # Border cells can only be walls
            if x == 0 or x == _width - 1 or y == 0 or y == _height - 1:
                cell.possible_tiles = [Tile.WALL]
                cell.collapse(_rng)
            row.append(cell)
        _cells.append(row)

    # Propagate from border
    for y in _height:
        for x in _width:
            if _cells[y][x].collapsed:
                _propagate(Vector2i(x, y))


func _find_lowest_entropy() -> Vector2i:
    var min_entropy := 9999
    var candidates: Array[Vector2i] = []

    for y in _height:
        for x in _width:
            var cell := _cells[y][x]
            if cell.collapsed:
                continue
            if cell.entropy() < min_entropy:
                min_entropy = cell.entropy()
                candidates = [Vector2i(x, y)]
            elif cell.entropy() == min_entropy:
                candidates.append(Vector2i(x, y))

    if candidates.size() == 0:
        return Vector2i(-1, -1)

    # Random tie-breaking
    return candidates[_rng.randi_range(0, candidates.size() - 1)]


func _propagate(start: Vector2i) -> void:
    ## Constraint propagation using a work queue.
    var queue: Array[Vector2i] = [start]
    var directions := [Vector2i(0, -1), Vector2i(1, 0), Vector2i(0, 1), Vector2i(-1, 0)]

    while queue.size() > 0:
        var pos := queue.pop_front()
        var cell := _cells[pos.y][pos.x]

        for dir_idx in 4:
            var neighbor_pos := pos + directions[dir_idx]
            if neighbor_pos.x < 0 or neighbor_pos.x >= _width:
                continue
            if neighbor_pos.y < 0 or neighbor_pos.y >= _height:
                continue

            var neighbor := _cells[neighbor_pos.y][neighbor_pos.x]
            if neighbor.collapsed:
                continue

            # Calculate valid tiles for neighbor based on current cell
            var valid_for_neighbor: Array[int] = []
            var opposite_dir := (dir_idx + 2) % 4  # Opposite direction

            for n_tile in neighbor.possible_tiles:
                var is_valid := false
                for c_tile in cell.possible_tiles:
                    if _adjacency_rules.has(c_tile):
                        var allowed: Array = _adjacency_rules[c_tile][dir_idx]
                        if n_tile in allowed:
                            is_valid = true
                            break
                if is_valid:
                    valid_for_neighbor.append(n_tile)

            # If we reduced possibilities, propagate further
            if valid_for_neighbor.size() < neighbor.possible_tiles.size():
                neighbor.possible_tiles = valid_for_neighbor
                if neighbor.possible_tiles.size() == 1:
                    neighbor.collapsed = true
                    neighbor.tile = neighbor.possible_tiles[0]
                if neighbor_pos not in queue:
                    queue.append(neighbor_pos)


func _all_collapsed() -> bool:
    for y in _height:
        for x in _width:
            if not _cells[y][x].collapsed:
                return false
    return true


func _build_output() -> Array[Array]:
    var output: Array[Array] = []
    for y in _height:
        var row: Array[int] = []
        for x in _width:
            row.append(_cells[y][x].tile if _cells[y][x].collapsed else Tile.WALL)
        output.append(row)
    return output
```

### 4.5 Choosing an Algorithm

| Algorithm | Best For | Pros | Cons |
|-----------|----------|------|------|
| BSP | Traditional dungeons | Non-overlapping rooms, structured | Can feel formulaic |
| Cellular Automata | Caves, organic spaces | Natural feel, simple | May need connectivity fix |
| Room-and-Corridor | Flexible roguelikes | Easy to customize, good loops | Needs overlap checks |
| WFC | Tile-based levels | Respects tile adjacency rules | Complex setup, can fail |
| Hybrid (BSP + CA) | Best of both worlds | Structured rooms + organic caves | More code to maintain |

**Recommended for a roguelike**: Room-and-Corridor as primary, with Cellular Automata for special cave floors, and BSP as a fallback algorithm.

---

## 5. Tilemap System & Navigation

### TileMapLayer (Godot 4.3+)

The `TileMap` node is **deprecated** as of Godot 4.3. Use `TileMapLayer` instead.

```gdscript
# Scene structure for a dungeon floor:
# DungeonFloor (Node2D)
#   +-- GroundLayer (TileMapLayer)    # Floor tiles
#   +-- WallLayer (TileMapLayer)       # Wall tiles (collision)
#   +-- DecorationLayer (TileMapLayer) # Props, decorations
#   +-- NavigationRegion2D             # For NavMesh pathfinding
#   +-- Entities (Node2D)              # Player, enemies, items
```

### Programmatic Tile Placement

```gdscript
class_name DungeonRenderer
extends Node2D

## Renders a 2D grid array to TileMapLayers.

@onready var ground_layer: TileMapLayer = $GroundLayer
@onready var wall_layer: TileMapLayer = $WallLayer

# TileSet source IDs and atlas coordinates (configure in TileSet editor)
const TILESET_SOURCE := 0
const FLOOR_ATLAS := Vector2i(0, 0)
const WALL_ATLAS := Vector2i(1, 0)
const WALL_TOP_ATLAS := Vector2i(1, 1)
const CORRIDOR_ATLAS := Vector2i(2, 0)


func render_grid(grid: Array[Array]) -> void:
    ground_layer.clear()
    wall_layer.clear()

    for y in grid.size():
        for x in grid[y].size():
            var cell_pos := Vector2i(x, y)
            if grid[y][x] == 0:  # Floor
                ground_layer.set_cell(cell_pos, TILESET_SOURCE, FLOOR_ATLAS)
            else:  # Wall
                wall_layer.set_cell(cell_pos, TILESET_SOURCE, WALL_ATLAS)
                # Add wall top decoration if floor is below
                if y + 1 < grid.size() and grid[y + 1][x] == 0:
                    wall_layer.set_cell(
                        Vector2i(x, y),
                        TILESET_SOURCE,
                        WALL_TOP_ATLAS
                    )


func world_to_grid(world_pos: Vector2) -> Vector2i:
    return ground_layer.local_to_map(world_pos)


func grid_to_world(grid_pos: Vector2i) -> Vector2:
    return ground_layer.map_to_local(grid_pos)
```

### AStarGrid2D Pathfinding

AStarGrid2D is the most efficient pathfinding for grid-based games. It is NOT connected to your TileMap -- you must sync them manually.

```gdscript
class_name DungeonPathfinder
extends RefCounted

## Wraps AStarGrid2D for dungeon pathfinding.
## Must be initialized AFTER the dungeon grid is generated.

var _astar := AStarGrid2D.new()
var _grid_size: Vector2i
var _cell_size: Vector2i


func initialize(grid: Array[Array], cell_size: Vector2i = Vector2i(16, 16)) -> void:
    _cell_size = cell_size
    _grid_size = Vector2i(grid[0].size(), grid.size())

    _astar.size = _grid_size
    _astar.cell_size = Vector2(cell_size)
    _astar.offset = Vector2(cell_size) / 2.0  # Center of tile
    _astar.diagonal_mode = AStarGrid2D.DIAGONAL_MODE_ONLY_IF_NO_OBSTACLES
    _astar.default_estimate_heuristic = AStarGrid2D.HEURISTIC_MANHATTAN
    _astar.update()

    # Mark walls as solid
    for y in grid.size():
        for x in grid[y].size():
            if grid[y][x] == 1:  # Wall
                _astar.set_point_solid(Vector2i(x, y), true)


func get_path_world(from_world: Vector2, to_world: Vector2) -> PackedVector2Array:
    ## Returns a path in world coordinates.
    var from_grid := _world_to_grid(from_world)
    var to_grid := _world_to_grid(to_world)

    # Clamp to bounds
    from_grid = from_grid.clamp(Vector2i.ZERO, _grid_size - Vector2i.ONE)
    to_grid = to_grid.clamp(Vector2i.ZERO, _grid_size - Vector2i.ONE)

    # Snap to nearest walkable cell if standing on a wall
    from_grid = _find_nearest_walkable(from_grid)
    to_grid = _find_nearest_walkable(to_grid)

    return _astar.get_point_path(from_grid, to_grid)


func get_path_grid(from: Vector2i, to: Vector2i) -> PackedVector2Array:
    ## Returns a path in grid coordinates.
    return _astar.get_point_path(from, to)


func is_walkable(grid_pos: Vector2i) -> bool:
    if not _astar.is_in_boundsv(grid_pos):
        return false
    return not _astar.is_point_solid(grid_pos)


func set_obstacle(grid_pos: Vector2i, solid: bool) -> void:
    ## Dynamically add/remove obstacles (e.g., doors, destructible walls).
    if _astar.is_in_boundsv(grid_pos):
        _astar.set_point_solid(grid_pos, solid)


func _world_to_grid(world_pos: Vector2) -> Vector2i:
    return Vector2i(world_pos / Vector2(_cell_size))


func _find_nearest_walkable(pos: Vector2i) -> Vector2i:
    if _astar.is_in_boundsv(pos) and not _astar.is_point_solid(pos):
        return pos
    # Search in expanding ring
    for radius in range(1, 5):
        for dy in range(-radius, radius + 1):
            for dx in range(-radius, radius + 1):
                var candidate := pos + Vector2i(dx, dy)
                if _astar.is_in_boundsv(candidate) and not _astar.is_point_solid(candidate):
                    return candidate
    return pos
```

### Enemy Pathfinding Integration

```gdscript
class_name EnemyController
extends CharacterBody2D

## Enemy that uses AStarGrid2D pathfinding to chase the player.

@export var speed: float = 80.0
@export var chase_range: float = 200.0
@export var path_recalc_interval: float = 0.3

var _path: PackedVector2Array = PackedVector2Array()
var _path_index: int = 0
var _path_timer: float = 0.0
var _pathfinder: DungeonPathfinder  # Injected by dungeon manager
var _target: Node2D = null  # The player


func setup(pathfinder: DungeonPathfinder, target: Node2D) -> void:
    _pathfinder = pathfinder
    _target = target


func _physics_process(delta: float) -> void:
    if _target == null or _pathfinder == null:
        return

    _path_timer -= delta
    var dist_to_player := global_position.distance_to(_target.global_position)

    # Only chase if within range
    if dist_to_player > chase_range:
        velocity = Vector2.ZERO
        move_and_slide()
        return

    # Recalculate path periodically
    if _path_timer <= 0.0:
        _path_timer = path_recalc_interval
        _path = _pathfinder.get_path_world(global_position, _target.global_position)
        _path_index = 0

    # Follow path
    if _path_index < _path.size():
        var target_pos := _path[_path_index]
        var direction := (target_pos - global_position).normalized()
        velocity = direction * speed

        if global_position.distance_to(target_pos) < 4.0:
            _path_index += 1
    else:
        velocity = Vector2.ZERO

    move_and_slide()
```

---

## 6. Game Programming Patterns in GDScript

### 6.1 State Machine (Node-Based)

The definitive pattern for managing entity behavior in Godot.

```gdscript
# --- state.gd ---
class_name State
extends Node

## Virtual base class for all states.

signal finished(next_state_path: String, data: Dictionary)


func handle_input(_event: InputEvent) -> void:
    pass


func update(_delta: float) -> void:
    pass


func physics_update(_delta: float) -> void:
    pass


func enter(_previous_state_path: String, _data: Dictionary = {}) -> void:
    pass


func exit() -> void:
    pass
```

```gdscript
# --- state_machine.gd ---
class_name StateMachine
extends Node

@export var initial_state: State = null

@onready var state: State = initial_state if initial_state else get_child(0) as State


func _ready() -> void:
    # Connect all child states
    for state_node: State in find_children("*", "State"):
        state_node.finished.connect(_transition_to_next_state)

    # Wait for owner to be ready
    await owner.ready
    state.enter("")


func _unhandled_input(event: InputEvent) -> void:
    state.handle_input(event)


func _process(delta: float) -> void:
    state.update(delta)


func _physics_process(delta: float) -> void:
    state.physics_update(delta)


func _transition_to_next_state(target_state_path: String, data: Dictionary = {}) -> void:
    if not has_node(target_state_path):
        printerr(owner.name + ": Trying to transition to state " + target_state_path + " but it does not exist.")
        return

    var previous_state_path := state.name
    state.exit()
    state = get_node(target_state_path)
    state.enter(previous_state_path, data)
```

Scene tree for a stateful enemy:
```
Slime (CharacterBody2D)
  +-- Sprite2D
  +-- CollisionShape2D
  +-- StateMachine
  |     +-- Idle (extends State)
  |     +-- Patrol (extends State)
  |     +-- Chase (extends State)
  |     +-- Attack (extends State)
  |     +-- Hurt (extends State)
  |     +-- Dead (extends State)
  +-- HurtboxComponent (Area2D)
  +-- HitboxComponent (Area2D)
```

### 6.2 Event Bus (Global Signal Singleton)

```gdscript
# --- events.gd --- (register as Autoload named "Events")
extends Node

## Global event bus for decoupled communication between systems.
## Use for events where emitter and receiver are far apart in the tree.
## Do NOT overuse -- direct signals are better for parent-child communication.

# --- Player Events ---
signal player_spawned(position: Vector2)
signal player_died
signal player_health_changed(current: int, maximum: int)
signal player_leveled_up(new_level: int)

# --- Combat Events ---
signal damage_dealt(amount: int, position: Vector2, is_critical: bool)
signal enemy_killed(enemy_type: String, position: Vector2)
signal all_enemies_cleared

# --- Dungeon Events ---
signal floor_generated(floor_number: int)
signal room_entered(room_data: Dictionary)
signal room_cleared(room_data: Dictionary)
signal exit_reached

# --- Item Events ---
signal item_collected(item_data: Resource)
signal gold_collected(amount: int)
signal item_used(item_data: Resource)

# --- UI Events ---
signal tooltip_requested(text: String, position: Vector2)
signal tooltip_dismissed
signal screen_shake_requested(intensity: float, duration: float)
signal notification_requested(message: String)
```

### 6.3 Object Pool

```gdscript
# --- object_pool.gd ---
class_name ObjectPool
extends Node

## Generic object pool. Pre-instantiates scenes and recycles them.
## Attach as a child node where pooled objects should live.

@export var pooled_scene: PackedScene
@export var initial_size: int = 20
@export var can_grow: bool = true

var _available: Array[Node] = []
var _all: Array[Node] = []


func _ready() -> void:
    for i in initial_size:
        _create_instance()


func get_instance() -> Node:
    ## Returns an available pooled instance, or creates one if pool can grow.
    var instance: Node = null

    if _available.size() > 0:
        instance = _available.pop_back()
    elif can_grow:
        instance = _create_instance()
    else:
        push_warning("ObjectPool: Pool exhausted and cannot grow!")
        return null

    instance.visible = true
    instance.process_mode = Node.PROCESS_MODE_INHERIT
    return instance


func release(instance: Node) -> void:
    ## Returns an instance to the pool for reuse.
    if instance not in _all:
        push_error("ObjectPool: Tried to release an instance that doesn't belong to this pool!")
        return

    instance.visible = false
    instance.process_mode = Node.PROCESS_MODE_DISABLED

    # Reset physics if applicable
    if instance is RigidBody2D:
        instance.linear_velocity = Vector2.ZERO
        instance.angular_velocity = 0.0

    if instance not in _available:
        _available.append(instance)


func release_all() -> void:
    for instance in _all:
        release(instance)


func _create_instance() -> Node:
    var instance := pooled_scene.instantiate()
    instance.visible = false
    instance.process_mode = Node.PROCESS_MODE_DISABLED
    add_child(instance)
    _all.append(instance)
    _available.append(instance)
    return instance
```

Usage:
```gdscript
# In your weapon/spawner:
@onready var bullet_pool: ObjectPool = $BulletPool

func shoot() -> void:
    var bullet := bullet_pool.get_instance() as Bullet
    if bullet:
        bullet.global_position = muzzle.global_position
        bullet.direction = aim_direction
        bullet.activate()

# In the bullet itself:
func _on_lifetime_expired() -> void:
    # Return to pool instead of queue_free()
    var pool := get_parent() as ObjectPool
    if pool:
        pool.release(self)
```

### 6.4 Component Pattern

```gdscript
# --- health_component.gd ---
class_name HealthComponent
extends Node

## Reusable health component. Attach to any entity that can take damage.

signal health_changed(current: int, maximum: int)
signal died

@export var max_health: int = 100

var current_health: int


func _ready() -> void:
    current_health = max_health


func take_damage(amount: int) -> void:
    current_health = maxi(0, current_health - amount)
    health_changed.emit(current_health, max_health)
    if current_health <= 0:
        died.emit()


func heal(amount: int) -> void:
    current_health = mini(max_health, current_health + amount)
    health_changed.emit(current_health, max_health)


func is_alive() -> bool:
    return current_health > 0
```

```gdscript
# --- hitbox_component.gd ---
class_name HitboxComponent
extends Area2D

## Damage-dealing area. Detects HurtboxComponents and applies damage.

@export var damage: int = 10
@export var knockback_force: float = 200.0


func _ready() -> void:
    area_entered.connect(_on_area_entered)


func _on_area_entered(area: Area2D) -> void:
    if area is HurtboxComponent:
        var hurtbox := area as HurtboxComponent
        hurtbox.take_hit(damage, knockback_force, global_position)
```

```gdscript
# --- hurtbox_component.gd ---
class_name HurtboxComponent
extends Area2D

## Damage-receiving area. Forwards hits to HealthComponent.

signal hit_received(damage: int, knockback: Vector2)

@export var health_component: HealthComponent
@export var invincibility_duration: float = 0.5

var _is_invincible := false


func take_hit(damage: int, knockback_force: float, source_pos: Vector2) -> void:
    if _is_invincible:
        return

    var knockback_dir := (owner.global_position - source_pos).normalized()
    hit_received.emit(damage, knockback_dir * knockback_force)

    if health_component:
        health_component.take_damage(damage)

    _start_invincibility()


func _start_invincibility() -> void:
    _is_invincible = true
    # Flash effect
    if owner.has_node("Sprite2D"):
        var sprite := owner.get_node("Sprite2D") as Sprite2D
        var tween := create_tween()
        tween.tween_property(sprite, "modulate:a", 0.3, 0.1)
        tween.tween_property(sprite, "modulate:a", 1.0, 0.1)
        tween.set_loops(int(invincibility_duration / 0.2))

    await get_tree().create_timer(invincibility_duration).timeout
    _is_invincible = false
```

### 6.5 Command Pattern (for Undo/Redo and Input Buffering)

```gdscript
# --- command.gd ---
class_name Command
extends RefCounted

## Base command for action queueing and replay.

func execute(_actor: Node) -> void:
    pass

func undo(_actor: Node) -> void:
    pass
```

```gdscript
# --- move_command.gd ---
class_name MoveCommand
extends Command

var direction: Vector2i

func _init(dir: Vector2i) -> void:
    direction = dir

func execute(actor: Node) -> void:
    if actor is CharacterBody2D:
        actor.position += Vector2(direction) * 16  # Tile size
```

```gdscript
# --- input_buffer.gd ---
class_name InputBuffer
extends RefCounted

## Buffers input commands for frame-perfect action queuing.

var _buffer: Array[Command] = []
var _buffer_time: float = 0.15  # 150ms buffer window
var _timers: Array[float] = []


func add(command: Command) -> void:
    _buffer.append(command)
    _timers.append(_buffer_time)


func consume() -> Command:
    if _buffer.size() > 0:
        var cmd := _buffer.pop_front()
        _timers.pop_front()
        return cmd
    return null


func update(delta: float) -> void:
    var i := _timers.size() - 1
    while i >= 0:
        _timers[i] -= delta
        if _timers[i] <= 0.0:
            _timers.remove_at(i)
            _buffer.remove_at(i)
        i -= 1


func has_buffered() -> bool:
    return _buffer.size() > 0
```

---

## 7. Mobile Optimization Techniques

### Rendering Optimization

```
# Project Settings for Mobile (Godot 4.4+)
rendering/renderer/rendering_method = "mobile"  # or "gl_compatibility" for older devices
rendering/textures/canvas_textures/default_texture_filter = "Nearest"  # For pixel art
rendering/2d/snap/snap_2d_transforms_to_pixel = true
rendering/2d/snap/snap_2d_vertices_to_pixel = true
```

### Draw Call Reduction

| Technique | Impact | How |
|-----------|--------|-----|
| Texture Atlases | High | Combine sprites into atlas sheets |
| TileMapLayer batching | High | Automatic in 4.4+ with Forward+/Mobile backends |
| Minimize unique materials | Medium | Share materials between similar objects |
| Limit ParallaxLayers | Medium | Max 2 layers on mobile |
| Avoid PanelContainer | Medium | Use simpler containers (known engine issue) |

### Physics Optimization

```gdscript
# Reduce physics tick rate if precision isn't critical
# Project Settings:
# physics/common/physics_ticks_per_second = 30  (default: 60)

# Use simpler collision shapes
# Circle > Rectangle > Capsule > Polygon (in order of cheapness)

# Disable physics for off-screen entities
func _on_visible_on_screen_notifier_2d_screen_exited() -> void:
    process_mode = Node.PROCESS_MODE_DISABLED

func _on_visible_on_screen_notifier_2d_screen_entered() -> void:
    process_mode = Node.PROCESS_MODE_INHERIT
```

### Memory Management

```gdscript
# Preload frequently used resources
const BULLET_SCENE := preload("res://scenes/entities/bullet.tscn")
const HIT_EFFECT := preload("res://scenes/effects/hit_effect.tscn")

# Load heavy resources asynchronously
func _load_dungeon_assets() -> void:
    ResourceLoader.load_threaded_request("res://tilesets/dungeon_tileset.tres")

    # Check status in _process or via timer
    var status := ResourceLoader.load_threaded_get_status("res://tilesets/dungeon_tileset.tres")
    if status == ResourceLoader.THREAD_LOAD_LOADED:
        var tileset := ResourceLoader.load_threaded_get("res://tilesets/dungeon_tileset.tres")

# Free resources explicitly when done
func _on_floor_completed() -> void:
    # Clear current dungeon
    for child in entities_container.get_children():
        child.queue_free()

    # Force garbage collection for large levels
    # (Godot does this automatically, but you can hint)
```

### GPU Particles on Mobile

```gdscript
# Keep particle FPS low
# GPUParticles2D -> Fixed FPS: 30 (NEVER higher on mobile)
# Prefer AnimatedSprite2D for simple effects

# Alternative: CPU particles for small counts
# CPUParticles2D is often better for < 50 particles on mobile
```

### Processing Budget

```gdscript
# Spread expensive work across frames
class_name FrameBudget
extends RefCounted

## Distributes work across multiple frames to maintain framerate.

var _work_queue: Array[Callable] = []
var _budget_ms: float = 2.0  # Max ms per frame for background work


func add_work(work: Callable) -> void:
    _work_queue.append(work)


func process() -> void:
    var start := Time.get_ticks_msec()
    while _work_queue.size() > 0:
        if Time.get_ticks_msec() - start > _budget_ms:
            break  # Over budget, continue next frame
        var work := _work_queue.pop_front()
        work.call()


func is_done() -> bool:
    return _work_queue.size() == 0
```

### Optimization Checklist for Mobile

- [ ] Use `Compatibility` or `Mobile` renderer (not `Forward+`)
- [ ] Texture atlas for all sprite sheets
- [ ] Object pool for bullets, effects, enemies
- [ ] GPUParticles2D fixed FPS = 30
- [ ] Max 2 ParallaxLayers
- [ ] Disable processing on off-screen nodes (VisibleOnScreenNotifier2D)
- [ ] Use `distance_squared_to()` instead of `distance_to()`
- [ ] Cache `@onready` references, never call `get_node()` in `_process()`
- [ ] Use typed arrays and static typing everywhere
- [ ] Profile with Godot's built-in profiler before optimizing
- [ ] Test on actual hardware (not simulator)

---

## 8. iOS-Specific Considerations

### Godot 4.4+ Metal Backend

Godot 4.4 introduced a **native Metal rendering backend** for Apple platforms. Metal is now the default renderer on iOS and macOS.

- Works on all Apple Silicon devices (all modern iOS devices)
- Faster than the previous MoltenVK (Vulkan-over-Metal) approach
- Vulkan via MoltenVK is still available as fallback in Project Settings

### iOS Export Setup

```
# Project Settings for iOS:
rendering/renderer/rendering_method = "mobile"  # Best for iOS
display/window/size/viewport_width = 1170       # iPhone 15 Pro
display/window/size/viewport_height = 2532
display/window/stretch/mode = "canvas_items"
display/window/stretch/aspect = "expand"
```

Export Steps:
1. **Editor -> Export -> Add iOS preset**
2. Configure bundle identifier, version, icons
3. **Export as Xcode project** (not PCK for standalone)
4. Open in Xcode, configure signing, build

### SwiftGodotKit Integration

For embedding Godot in a native Swift app (e.g., if you have a Swift shell app):

```swift
// 1. Add SwiftGodotKit via Swift Package Manager
// 2. Configure build settings:
//    - Other Linker Flags: -lc++
//    - Add MetalFX.framework to linked frameworks

import SwiftGodotKit
import SwiftGodot

// 3. Create the app with PCK file
@State var godotApp: GodotApp = GodotApp(packFile: "main.pck")

var body: some View {
    GodotAppView()
        .environment(\.godotApp, godotApp)
}
```

Swift-Godot Communication:
```swift
// Swift side: Messenger singleton
@Godot
class GameMessenger: Object {
    static let shared = GameMessenger()
    @Signal var settingsChanged: SignalWithArguments<String>
}

// Register in initHookCb at .scene level
```

```gdscript
# GDScript side: Receive from Swift
func _ready() -> void:
    var messenger := Engine.get_singleton("GameMessenger")
    if messenger:
        messenger.settings_changed.connect(_on_settings_changed)
```

### PCK Export for Embedding

```bash
# Command-line export (for CI/CD)
# Create symlink first:
sudo ln -s "/Applications/Godot.app/Contents/MacOS/Godot" /usr/local/bin/godot

# Export PCK:
godot --headless --export-pack "iOS" /path/to/output/main.pck
```

### iOS Gotchas and Workarounds

| Issue | Workaround |
|-------|-----------|
| No iOS Simulator support | Must test on physical device |
| MetalFX framework missing error | Manually add MetalFX.framework in Xcode |
| Binary size ~30MB from Godot | Acceptable; use asset compression |
| `print()` in GDScript | Outputs to Xcode console (useful for debugging) |
| camelCase/snake_case | SwiftGodotKit auto-maps conventions |
| Input differences | Use `InputEventScreenTouch` and `InputEventScreenDrag` |
| No file system write on iOS | Use `user://` path for save data |
| App Store privacy declarations | Declare all Godot networking if used |

### iOS Input Handling

```gdscript
# Touch input for mobile roguelike
func _unhandled_input(event: InputEvent) -> void:
    if event is InputEventScreenTouch:
        var touch := event as InputEventScreenTouch
        if touch.pressed:
            _handle_tap(touch.position)

    elif event is InputEventScreenDrag:
        var drag := event as InputEventScreenDrag
        _handle_swipe(drag.relative)


func _handle_tap(screen_pos: Vector2) -> void:
    # Convert screen position to world position
    var world_pos := get_canvas_transform().affine_inverse() * screen_pos
    # Move player toward tapped position
    Events.move_requested.emit(world_pos)


func _handle_swipe(relative: Vector2) -> void:
    # Determine swipe direction for grid-based movement
    if relative.length() < 10.0:
        return

    var angle := relative.angle()
    var direction := Vector2i.ZERO

    if angle > -PI/4 and angle < PI/4:
        direction = Vector2i.RIGHT
    elif angle > PI/4 and angle < 3*PI/4:
        direction = Vector2i.DOWN
    elif angle > -3*PI/4 and angle < -PI/4:
        direction = Vector2i.UP
    else:
        direction = Vector2i.LEFT

    Events.direction_input.emit(direction)
```

---

## 9. Roguelike Project Structure Template

### Directory Structure

```
res://
+-- project.godot
+-- export_presets.cfg
+-- .gitignore
|
+-- assets/
|   +-- sprites/
|   |   +-- player/
|   |   +-- enemies/
|   |   +-- items/
|   |   +-- tiles/
|   |   +-- effects/
|   |   +-- ui/
|   +-- audio/
|   |   +-- music/
|   |   +-- sfx/
|   +-- fonts/
|   +-- shaders/
|
+-- scenes/
|   +-- main.tscn                    # Entry point
|   +-- game/
|   |   +-- game.tscn               # Active gameplay container
|   |   +-- game.gd
|   |   +-- dungeon_floor.tscn      # Single dungeon floor
|   |   +-- dungeon_floor.gd
|   +-- entities/
|   |   +-- player/
|   |   |   +-- player.tscn
|   |   |   +-- player.gd
|   |   |   +-- player_states/
|   |   |       +-- idle.gd
|   |   |       +-- move.gd
|   |   |       +-- attack.gd
|   |   |       +-- hurt.gd
|   |   +-- enemies/
|   |   |   +-- base_enemy.tscn
|   |   |   +-- base_enemy.gd
|   |   |   +-- slime/
|   |   |   +-- skeleton/
|   |   |   +-- boss/
|   |   +-- items/
|   |   |   +-- pickup.tscn
|   |   |   +-- chest.tscn
|   |   +-- projectiles/
|   |       +-- bullet.tscn
|   +-- effects/
|   |   +-- hit_effect.tscn
|   |   +-- death_effect.tscn
|   |   +-- damage_number.tscn
|   +-- ui/
|   |   +-- hud.tscn
|   |   +-- pause_menu.tscn
|   |   +-- game_over.tscn
|   |   +-- inventory.tscn
|   |   +-- minimap.tscn
|   +-- menus/
|       +-- title_screen.tscn
|       +-- settings.tscn
|       +-- character_select.tscn
|
+-- scripts/
|   +-- autoloads/
|   |   +-- events.gd               # Global signal bus
|   |   +-- game_state.gd           # Persistent run data
|   |   +-- audio_manager.gd        # Audio singleton
|   |   +-- save_manager.gd         # Save/load system
|   +-- components/
|   |   +-- health_component.gd
|   |   +-- hitbox_component.gd
|   |   +-- hurtbox_component.gd
|   |   +-- movement_component.gd
|   |   +-- loot_drop_component.gd
|   +-- patterns/
|   |   +-- state.gd
|   |   +-- state_machine.gd
|   |   +-- object_pool.gd
|   |   +-- command.gd
|   +-- generation/
|   |   +-- bsp_dungeon.gd
|   |   +-- cave_generator.gd
|   |   +-- room_corridor_dungeon.gd
|   |   +-- dungeon_populator.gd     # Places enemies, items, etc.
|   |   +-- room_templates.gd        # Pre-designed room features
|   +-- systems/
|   |   +-- dungeon_pathfinder.gd
|   |   +-- dungeon_renderer.gd
|   |   +-- loot_table.gd
|   |   +-- progression.gd
|   |   +-- difficulty_scaler.gd
|
+-- resources/
|   +-- items/
|   |   +-- sword_basic.tres
|   |   +-- potion_health.tres
|   +-- enemies/
|   |   +-- enemy_slime.tres
|   |   +-- enemy_skeleton.tres
|   +-- loot_tables/
|   |   +-- floor_1_loot.tres
|   |   +-- boss_loot.tres
|   +-- tilesets/
|       +-- dungeon_tileset.tres
|
+-- addons/                          # Third-party plugins
```

### Autoload Registration Order

```
# Project Settings -> Autoload (order matters):
1. Events        -> res://scripts/autoloads/events.gd
2. GameState     -> res://scripts/autoloads/game_state.gd
3. AudioManager  -> res://scripts/autoloads/audio_manager.gd
4. SaveManager   -> res://scripts/autoloads/save_manager.gd
```

### GameState Autoload

```gdscript
# --- game_state.gd ---
extends Node

## Persistent state for the current roguelike run.

# Run state
var current_floor: int = 1
var gold: int = 0
var kill_count: int = 0
var run_time: float = 0.0
var is_run_active: bool = false

# Player persistent state
var player_max_health: int = 100
var player_current_health: int = 100
var player_level: int = 1
var player_xp: int = 0
var inventory: Array[Resource] = []
var equipped_weapon: Resource = null

# Dungeon state
var dungeon_seed: int = -1
var rooms_cleared: int = 0
var floor_map: Array[Array] = []

# Meta progression (persists across runs)
var total_runs: int = 0
var best_floor: int = 0
var total_gold_earned: int = 0
var unlocked_characters: Array[String] = ["warrior"]


func start_new_run(character: String = "warrior") -> void:
    current_floor = 1
    gold = 0
    kill_count = 0
    run_time = 0.0
    is_run_active = true
    player_current_health = player_max_health
    player_level = 1
    player_xp = 0
    inventory.clear()
    equipped_weapon = null
    rooms_cleared = 0
    dungeon_seed = randi()
    total_runs += 1


func end_run() -> void:
    is_run_active = false
    best_floor = maxi(best_floor, current_floor)
    total_gold_earned += gold
    SaveManager.save_meta_progression()


func advance_floor() -> void:
    current_floor += 1
    rooms_cleared = 0
    dungeon_seed = randi()  # New seed per floor
```

### Main Scene Flow

```gdscript
# --- main.gd ---
extends Node

## Root scene managing game flow: menus -> gameplay -> game over.

@onready var _current_scene: Node = null


func _ready() -> void:
    Events.connect("exit_reached", _on_exit_reached)
    Events.connect("player_died", _on_player_died)
    _load_scene("res://scenes/menus/title_screen.tscn")


func _load_scene(path: String) -> void:
    if _current_scene:
        _current_scene.queue_free()
        await _current_scene.tree_exited

    var scene := load(path) as PackedScene
    _current_scene = scene.instantiate()
    add_child(_current_scene)


func start_game() -> void:
    GameState.start_new_run()
    _load_scene("res://scenes/game/game.tscn")


func _on_exit_reached() -> void:
    GameState.advance_floor()
    # Reload dungeon floor (game.gd handles floor generation)
    _load_scene("res://scenes/game/game.tscn")


func _on_player_died() -> void:
    GameState.end_run()
    _load_scene("res://scenes/ui/game_over.tscn")
```

---

## 10. Godot 4.4+ Feature Reference

### Key Godot 4.4 Features

| Feature | Description | Impact |
|---------|-------------|--------|
| **Native Metal backend** | Direct Metal rendering on Apple Silicon | Better iOS performance |
| **2D Batching (all backends)** | Reduces draw calls for Forward+/Mobile | Major 2D perf improvement |
| **Typed Dictionaries** | `Dictionary[String, int]` syntax | Better type safety |
| **TileMapLayer (stable)** | Replaces deprecated TileMap node | Must use for new projects |
| **Physics interpolation (3D)** | Smoother physics rendering | 2D planned for later |
| **mbedTLS security fix** | Network vulnerability patch in 4.4.1 | Update if using networking |

### Key Godot 4.5 Features (Preview)

| Feature | Description |
|---------|-------------|
| **Abstract classes** | `@abstract` annotation for GDScript |
| **Shader baking** | Pre-compile shaders at export for faster startup |
| **visionOS support** | Apple Vision Pro export |
| **WebAssembly SIMD** | Better web export performance |

### Typed Dictionary Example (4.4+)

```gdscript
# Before 4.4: untyped
var loot_table = {}

# 4.4+: typed dictionary
var loot_table: Dictionary[String, float] = {
    "sword": 0.3,
    "shield": 0.2,
    "potion": 0.4,
    "gold": 0.1,
}

# Typed with custom resources
var enemy_registry: Dictionary[String, PackedScene] = {}
```

### Abstract Classes (4.5+)

```gdscript
# Prevents direct instantiation -- subclasses must implement
@abstract
class_name BaseEnemy
extends CharacterBody2D

@abstract
func get_attack_pattern() -> Array[Vector2i]:
    return []

@abstract
func get_loot_table() -> Resource:
    return null

# Concrete implementation
class_name Slime
extends BaseEnemy

func get_attack_pattern() -> Array[Vector2i]:
    return [Vector2i.UP, Vector2i.DOWN, Vector2i.LEFT, Vector2i.RIGHT]

func get_loot_table() -> Resource:
    return preload("res://resources/loot_tables/slime_loot.tres")
```

---

## 11. Performance Profiling & Measurement

### Using Godot's Built-in Profiler

1. Run your game
2. Open **Debugger** panel (bottom of editor)
3. Switch to **Profiler** tab
4. Click **Start** to begin recording
5. Identify functions consuming >2ms per frame

### Measuring Code Performance

```gdscript
# Manual timing for specific operations
func _benchmark_dungeon_generation() -> void:
    var start := Time.get_ticks_usec()

    var dungeon := BSPDungeon.new()
    var grid := dungeon.generate(80, 60, 42)

    var elapsed := Time.get_ticks_usec() - start
    print("Dungeon generation: %d microseconds (%.2f ms)" % [elapsed, elapsed / 1000.0])
```

### Performance Targets (Mobile)

| Metric | Target | Critical |
|--------|--------|----------|
| Frame time | < 16.6ms (60 FPS) | < 33.3ms (30 FPS) |
| Draw calls | < 100 | < 200 |
| Active nodes | < 500 | < 1000 |
| Physics bodies | < 100 | < 200 |
| Memory | < 200MB | < 400MB |

### Common Bottleneck Identification

```
Symptom: Low FPS, high CPU in profiler
  -> Check _process() functions for expensive operations
  -> Look for get_node() calls in _process (cache with @onready)
  -> Check for unnecessary Array allocations in loops

Symptom: Low FPS, GPU indicator high
  -> Too many draw calls (check with profiler)
  -> Use texture atlases and batching
  -> Reduce particle count
  -> Lower viewport resolution

Symptom: Stuttering/hitches
  -> Scene instantiation during gameplay (use object pools)
  -> Resource loading (use ResourceLoader.load_threaded_request)
  -> Large dungeon generation (spread across frames with FrameBudget)

Symptom: Memory growing over time
  -> Leaked nodes (not calling queue_free())
  -> Growing arrays not being cleared
  -> Resources loaded but never freed
```

### Key GDScript Performance Rules

1. **`for element in array` is faster than `for i in array.size()`** (~60% difference)
2. **`if/elif` is ~20% faster than `match`** for conditional chains
3. **`distance_squared_to()` avoids square root** -- always use for comparisons
4. **`pop_back()` is O(1), `pop_front()` is O(n)** -- use back for stacks
5. **Preload scenes, load resources** -- `preload()` is compile-time, `load()` is runtime
6. **Static typing generates optimized bytecode** -- type everything
7. **Built-in C++ functions beat GDScript equivalents** -- use engine features
8. **Avoid `print()` in production** -- it's expensive (string building + I/O)
9. **Cache calculations outside loops** -- don't recalculate constants per iteration
10. **Instance PackedScenes, don't `new()` + `add_child()`** -- 3-5x faster

---

## 12. Resource System & Data Management

### Custom Resources for Game Data

```gdscript
# --- item_data.gd ---
class_name ItemData
extends Resource

## Defines an item's properties. Create .tres files in the editor.

@export var item_name: String = ""
@export var description: String = ""
@export var icon: Texture2D
@export var rarity: Rarity = Rarity.COMMON
@export var value: int = 0

@export_group("Combat Stats")
@export var damage_bonus: int = 0
@export var defense_bonus: int = 0
@export var speed_bonus: float = 0.0
@export var health_bonus: int = 0

@export_group("Special Effects")
@export var on_hit_effect: PackedScene
@export var passive_effects: Array[StatusEffect] = []

enum Rarity { COMMON, UNCOMMON, RARE, EPIC, LEGENDARY }


func get_rarity_color() -> Color:
    match rarity:
        Rarity.COMMON: return Color.WHITE
        Rarity.UNCOMMON: return Color.GREEN
        Rarity.RARE: return Color.CORNFLOWER_BLUE
        Rarity.EPIC: return Color.PURPLE
        Rarity.LEGENDARY: return Color.GOLD
    return Color.WHITE
```

```gdscript
# --- enemy_data.gd ---
class_name EnemyData
extends Resource

## Enemy stat block. Create a .tres per enemy type.

@export var enemy_name: String = ""
@export var scene: PackedScene
@export var max_health: int = 50
@export var damage: int = 10
@export var speed: float = 60.0
@export var xp_reward: int = 10
@export var loot_table: LootTable
@export var first_floor_appearance: int = 1
```

```gdscript
# --- loot_table.gd ---
class_name LootTable
extends Resource

## Weighted loot drops. Each entry has an item and a weight.

@export var entries: Array[LootEntry] = []
@export var guaranteed_gold_min: int = 0
@export var guaranteed_gold_max: int = 5


func roll(rng: RandomNumberGenerator, count: int = 1) -> Array[ItemData]:
    var results: Array[ItemData] = []
    var total_weight := 0.0

    for entry in entries:
        total_weight += entry.weight

    for i in count:
        var roll := rng.randf() * total_weight
        var cumulative := 0.0
        for entry in entries:
            cumulative += entry.weight
            if roll <= cumulative:
                results.append(entry.item)
                break

    return results


func roll_gold(rng: RandomNumberGenerator) -> int:
    return rng.randi_range(guaranteed_gold_min, guaranteed_gold_max)
```

### Save/Load with Resources

```gdscript
# --- save_manager.gd --- (Autoload)
extends Node

const SAVE_PATH := "user://save_data.tres"
const META_PATH := "user://meta_progress.tres"


func save_run() -> void:
    var save_data := SaveData.new()
    save_data.floor = GameState.current_floor
    save_data.gold = GameState.gold
    save_data.health = GameState.player_current_health
    save_data.inventory = GameState.inventory.duplicate()
    save_data.dungeon_seed = GameState.dungeon_seed

    var error := ResourceSaver.save(save_data, SAVE_PATH)
    if error != OK:
        push_error("Failed to save: " + str(error))


func load_run() -> bool:
    if not FileAccess.file_exists(SAVE_PATH):
        return false

    # SECURITY WARNING: Only load resources from trusted sources.
    # Never load user-provided .tres files -- they can contain scripts.
    var save_data := load(SAVE_PATH) as SaveData
    if save_data == null:
        return false

    GameState.current_floor = save_data.floor
    GameState.gold = save_data.gold
    GameState.player_current_health = save_data.health
    GameState.inventory = save_data.inventory
    GameState.dungeon_seed = save_data.dungeon_seed
    return true


func save_meta_progression() -> void:
    var meta := MetaProgression.new()
    meta.total_runs = GameState.total_runs
    meta.best_floor = GameState.best_floor
    meta.total_gold_earned = GameState.total_gold_earned
    meta.unlocked_characters = GameState.unlocked_characters.duplicate()
    ResourceSaver.save(meta, META_PATH)


func delete_run_save() -> void:
    if FileAccess.file_exists(SAVE_PATH):
        DirAccess.remove_absolute(SAVE_PATH)
```

### Security Note on Resources

Resources can execute scripts when loaded. **Never** load `.tres` files from untrusted sources (user mods, network). For user-generated save data, prefer JSON:

```gdscript
# Safe alternative for untrusted data:
func save_as_json(data: Dictionary, path: String) -> void:
    var json_string := JSON.stringify(data, "  ")
    var file := FileAccess.open(path, FileAccess.WRITE)
    file.store_string(json_string)

func load_from_json(path: String) -> Dictionary:
    var file := FileAccess.open(path, FileAccess.READ)
    if file == null:
        return {}
    var json := JSON.new()
    json.parse(file.get_as_text())
    return json.data as Dictionary
```

---

## Quick Reference Card

### GDScript Cheat Sheet

```gdscript
# Typed variable declaration
var x: int = 10
var name: String = "Scotty"
var pos := Vector2.ZERO       # Type inferred

# Typed arrays and dictionaries (4.4+)
var enemies: Array[Enemy] = []
var stats: Dictionary[String, int] = {"hp": 100, "atk": 10}

# Null-safe operations
var health: int = entity.get("health", 0)  # Default if missing
var node := get_node_or_null("MaybeExists") as Sprite2D

# Signal declaration and emission
signal damaged(amount: int)
damaged.emit(25)

# Lambda / anonymous functions
var sorted := items.filter(func(i: ItemData) -> bool: return i.rarity >= ItemData.Rarity.RARE)
enemies.sort_custom(func(a: Enemy, b: Enemy) -> bool: return a.health < b.health)

# Await
await get_tree().create_timer(1.0).timeout
var result = await some_async_function()

# Match (Godot's switch)
match direction:
    Vector2i.UP: print("up")
    Vector2i.DOWN: print("down")
    _: print("other")

# Ternary
var label := "alive" if health > 0 else "dead"

# String formatting
print("Floor %d, HP: %d/%d" % [floor_num, current_hp, max_hp])
```

### Node Lifecycle Order

```
_init()         -> Constructor (no tree access)
_enter_tree()   -> Added to scene tree
_ready()        -> All children ready (called once)
_process()      -> Every visual frame
_physics_process() -> Every physics tick
_exit_tree()    -> Removed from scene tree
```

### Common Gotchas

1. `_ready()` is called ONCE -- if you re-add a node, use `_enter_tree()`
2. `@onready` variables are null in `_init()` -- only valid from `_ready()` onward
3. `queue_free()` is deferred -- node exists until end of frame
4. Signals connected in editor persist on scene reload; code connections don't
5. `preload()` must use literal string paths; `load()` accepts variables
6. `move_and_slide()` uses `velocity` property directly in Godot 4 (no parameter)
7. TileMap is deprecated -- use TileMapLayer (4.3+)
8. Typed arrays are NOT covariant -- `Array[Slime]` is NOT `Array[Enemy]`

---

*"The right tool for the right job, Captain. And in this case, the right engine."*
-- Scotty, Lead Game Developer, Forge&Code
