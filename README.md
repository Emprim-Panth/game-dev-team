# Game Development Team - Complete Knowledge Package
*Compiled from Forge&Code Starfleet Crew - December 2024*

This document contains the complete game development knowledge base created during the development of Oasis, a roguelike dungeon crawler. This represents expertise across game direction, architecture, development, balance, audio, and marketing.

---

## Table of Contents

1. [Overview](#overview)
2. [Team Roles](#team-roles)
3. [How to Use This Package](#how-to-use-this-package)
4. [Individual Discipline Deep Dives](#individual-discipline-deep-dives)

---

## Overview

This knowledge base was built during active development of a mobile roguelike dungeon crawler (Oasis). It represents lessons learned, frameworks adopted, and expertise developed across:

- **Game Direction & Production** - How to direct a game from concept to ship
- **Technical Architecture** - System design patterns for games in Godot 4.x
- **Implementation** - Practical GDScript development patterns
- **Game Balance & QA** - Mathematical frameworks for roguelike balance
- **Audio Design** - Music composition and sound design for games
- **Marketing & ASO** - App Store optimization and community building

**Tech Stack:**
- Engine: Godot 4.4+
- Language: GDScript (with performance-critical systems in Rust via GDExtension where needed)
- Platform: iOS/iPad first (via SwiftGodotKit), expandable to PC/Mac/Console

**Studio Philosophy:**
- Buy Once, Own Forever (premium pricing, no ads, no gacha)
- Mobile-first, session-friendly design
- Indie scale with AAA ambition in art and polish

---

## Team Roles

### Spock - Game Director / Producer
**Domain:** Creative vision, production oversight, design authority, scope management

**Core Responsibilities:**
- Define and protect the core experience
- Set milestones, track progress, manage scope
- Make final calls on mechanics, systems, content
- Quality bar definition
- Player advocacy

**Key Expertise:**
- Miyamoto's melody-first design principles
- Miyazaki's difficulty as teaching
- Sirlin's balance frameworks
- Roguelike design patterns (from Hades, Slay the Spire)
- Production methodologies (Agile adapted for games)
- Scope management (MoSCoW prioritization, feature budgets)

### Geordi - Technical Architect
**Domain:** System design, patterns, data-driven architecture, performance

**Core Responsibilities:**
- Define technical architecture and patterns
- Data-driven design systems
- Procedural generation architecture
- Save system design
- Cross-platform strategy
- Performance targets and optimization strategy

**Key Expertise:**
- Game programming patterns (from Nystrom's book)
- Godot-specific architecture (scene composition, autoloads, signals)
- Procedural generation algorithms (BSP, cellular automata, WFC)
- Data-driven systems via Custom Resources
- Mobile optimization strategies
- SwiftGodotKit integration for iOS

### Scotty - Lead Game Developer
**Domain:** Godot 4.x implementation, GDScript patterns, procedural generation

**Core Responsibilities:**
- Hands-on implementation
- GDScript best practices
- Godot node composition patterns
- Procedural dungeon generation code
- Tilemap and navigation systems
- Mobile-specific optimizations

**Key Expertise:**
- Godot 4.4+ feature set
- Component-based node design
- State machines and game flow
- AStarGrid2D pathfinding
- Object pooling and rendering optimization
- iOS-specific considerations (Metal, SwiftGodotKit)

### Worf - QA Lead & Balance Designer
**Domain:** Testing, balance, difficulty tuning, combat math

**Core Responsibilities:**
- Define test plans and coverage
- Balance all game systems
- Difficulty curve calibration
- Combat math and formulas
- Progression pacing
- Economy design

**Key Expertise:**
- RPG damage formulas (tested across multiple systems)
- Roguelike balance principles (from Slay the Spire's metrics-driven approach)
- DPS calculations and TTK targeting
- Stat scaling curves
- Loot table design and pity systems
- Mobile QA (touch input, interruption handling, device matrix)

### Vic (Composer) - Game Audio
**Domain:** Music composition, sound design, adaptive audio

**Core Responsibilities:**
- Compose full game soundtrack
- Design all sound effects
- Implement adaptive music systems
- Audio direction and identity

**Key Expertise:**
- Music theory for emotion (key signatures, modes, tempo)
- Iconic game soundtrack analysis (Kondo, Uematsu, Korb, Larkin)
- AI music generation (Suno/Udio) with post-production
- Adaptive music systems (vertical layering, horizontal resequencing)
- Godot audio implementation (AudioStreamInteractive, AudioStreamSynchronized)
- SFX categories and design principles

### Quark - Marketing & ASO Specialist
**Domain:** App Store Optimization, launch strategy, community building

**Core Responsibilities:**
- App Store metadata optimization
- Screenshot and preview video strategy
- Launch timeline coordination
- Community building
- Press and influencer outreach
- Pricing strategy

**Key Expertise:**
- ASO complete framework (keywords, metadata, conversion optimization)
- Launch strategy (90-day timeline, pre/launch/post phases)
- Marketing channel priority (TikTok, Reddit, Discord-first)
- Getting featured by Apple
- Premium game positioning in F2P market
- Reviews and ratings strategy

---

## How to Use This Package

### For New Game Developers

1. **Start with Spock (Game Director)** - Understand production methodology, scope management, and the game design pillars framework
2. **Move to Geordi (Architecture)** - Learn system design patterns before writing code
3. **Study Scotty (Implementation)** - Practical GDScript patterns and Godot-specific knowledge
4. **Reference Worf (Balance)** as you implement systems that need tuning
5. **Engage Vic (Audio)** when ready for music and sound
6. **Apply Quark (Marketing)** 6-8 weeks before launch

### For Specific Needs

| If you need... | Consult... |
|----------------|-----------|
| Help scoping a game idea | Spock - Section 6 (Scope Management) |
| Procedural generation algorithms | Scotty - Section 4, Geordi - Section 4 |
| Combat system design | Worf - Section 3 (RPG Combat Math) |
| App Store listing optimization | Quark - Section 2 (ASO Complete Guide) |
| Adaptive music implementation | Vic - Section 6 & 8 |
| State machine patterns | Scotty - Section 6, Geordi - Section 6 |
| Launch timeline | Quark - Section 3 |
| Damage formula selection | Worf - Section 3.1 |

### Adapting for Your Project

This knowledge base is built around:
- **Genre:** Roguelike dungeon crawler
- **Platform:** Mobile-first (iOS/iPad)
- **Business Model:** Premium ($2.99, no ads)
- **Engine:** Godot 4.x

**To adapt:**
- **Different genre:** Core patterns (state machines, component design) still apply. Game-specific balance math will differ.
- **Different platform:** Geordi's cross-platform architecture section covers this. Most patterns are platform-agnostic.
- **Different business model:** Quark's marketing section is premium-focused. F2P requires different ASO and monetization strategy.
- **Different engine:** Spock's direction principles, Worf's balance math, and Vic's audio theory are engine-agnostic. Implementation details from Scotty and Geordi are Godot-specific.

---

## Individual Discipline Deep Dives

The full knowledge base for each crew member is contained in the following files:

### 1. Spock - Game Director Knowledge Base
**File:** `spock-game-director.md`
**Size:** ~135 KB / ~9,000 lines
**Key Sections:**
- Role Definition: What a Game Director Actually Does
- The Director's Day-to-Day (phase-dependent)
- Decision-Making Frameworks (Vision Test, Priority Matrix, Two-Hat Rule)
- Lessons from Legendary Directors (Miyamoto, Miyazaki, Vincke, Blow, Kasavin)
- Production Pipeline (4 phases: Research, Pre-Production, Production, Wrap-Up)
- Scope Management & Feature Prioritization
- Common Pitfalls & How to Avoid Them
- Game Design Document Template
- Directing a Mobile Roguelike Dungeon Crawler
- The Director's Playbook (practical protocols)

**Critical Frameworks:**
- Feature Categories (Core, Fantasy, Constraints) x Risk Assessment (A-D)
- MoSCoW prioritization for features
- Scope creep detection signals
- Weekly director review checklist
- Playtest protocol
- Emergency protocols ("the game isn't fun", "we're going to miss the milestone")

### 2. Geordi - Technical Architecture Knowledge Base
**File:** `geordi-game-architect.md`
**Size:** ~170 KB / ~11,000 lines
**Key Sections:**
- Foundational Game Architecture Patterns (from Nystrom's book)
- Godot-Specific Architecture (scene tree, composition, autoloads)
- Data-Driven Design (Custom Resources, Type Object pattern)
- Procedural Generation Architecture (dungeon pipeline, seeded RNG)
- Save System Architecture (versioned schemas, auto-save)
- Game State Management (FSMs, game flow)
- Cross-Platform Architecture (iOS via SwiftGodotKit)
- Performance Architecture (frame budgets, object pooling)
- Plugin and Mod Architecture (portal API for extensibility)

**Critical Frameworks:**
- Three-layer architecture (Visual, Logic, Data)
- Component pattern for reusable behaviors
- DungeonData model for procedural generation
- Save schema migration chain
- Platform abstraction layer

### 3. Scotty - Lead Developer Knowledge Base
**File:** `scotty-game-developer.md`
**Size:** ~268 KB / ~17,000 lines
**Key Sections:**
- Godot 4 Architecture & Node System
- GDScript Patterns & Style Guide (file structure order)
- GDScript Anti-Patterns
- Procedural Dungeon Generation Algorithms (BSP, Cellular Automata, Room-and-Corridor, WFC)
- Tilemap System & Navigation (TileMapLayer, AStarGrid2D)
- Game Programming Patterns in GDScript
- Mobile Optimization Techniques
- iOS-Specific Considerations (Metal, SwiftGodotKit)
- Roguelike Project Structure Template
- Godot 4.4+ Feature Reference
- Performance Profiling & Measurement
- Resource System & Data Management

**Critical Code Patterns:**
- State machine implementation (node-based)
- Object pooling for mobile
- BSP dungeon generator (complete, tested)
- Cellular automata cave generator (complete)
- AStarGrid2D pathfinding wrapper
- Component-based entity design

### 4. Worf - QA Lead & Balance Designer Knowledge Base
**File:** `worf-game-balance.md`
**Size:** ~113 KB / ~7,500 lines
**Key Sections:**
- Role Definition (QA + Balance as unified discipline)
- Game Balance Frameworks & Philosophy (Sirlin, Slay the Spire metrics, Schreiber)
- RPG Combat Math (damage formulas, stat relationships, DPS calculations)
- Roguelike-Specific Balance Principles (run structure, power curves, RNG contract)
- Difficulty Design (Flow State Theory, modular difficulty)
- Progression & Economy Balance (XP curves, reward scheduling)
- QA Test Plan Template (functional, performance, mobile-specific, balance testing)
- 4-Class Roguelike Balance Checklist

**Critical Frameworks:**
- Damage formula comparison (5 options with pros/cons)
- TTK targets by encounter type
- Healing vs. Damage ratio thresholds
- Power budget per floor (player vs enemy scaling)
- Loot table architecture with pity systems
- Mobile QA test matrix (device, performance, interruption handling)

### 5. Vic - Game Composer & Sound Designer Knowledge Base
**File:** `vic-game-audio.md`
**Size:** ~138 KB / ~9,000 lines
**Key Sections:**
- Role Definitions (Composer, Sound Designer, Audio Director)
- Iconic Soundtrack Analysis (Zelda, Final Fantasy, Hades, DOOM, Hollow Knight, Celeste)
- Music Theory for Game Emotion (keys, modes, tempo, chord progressions, instrumentation)
- Genre Guide (when to use orchestral, dark ambient, dungeon synth, metal, folk, etc.)
- AI Music Generation Pipeline (Suno/Udio prompting + post-production)
- Adaptive Music System Design (vertical layering, horizontal resequencing)
- Sound Effect Design (categories, principles, implementation)
- Godot Audio Implementation (AudioStreamInteractive, bus architecture, SFX pooling)
- Dungeon Crawler Music Plan Template

**Critical Frameworks:**
- Key signature emotional associations (major and minor keys mapped to game moments)
- Modal theory for game music (Dorian for medieval, Phrygian for tension, Lydian for wonder)
- Tempo relationship design (exploration 80 BPM, combat 160 BPM = exact double)
- Adaptive music layer composition guide (6 layers from ambient to intensity spike)
- AI prompt templates by music type (exploration, combat, boss, safe room)
- Godot audio bus architecture

### 6. Quark - Marketing & ASO Knowledge Base
**File:** `quark-game-marketing.md`
**Size:** ~110 KB / ~7,500 lines
**Key Sections:**
- Role Definition: Game Marketing Specialist (mobile premium focus)
- App Store Optimization (ASO) Complete Guide (metadata stack, keyword research)
- Launch Strategy Timeline (8 weeks pre-launch through 12 weeks post)
- Marketing Channel Guide (priority matrix, Reddit strategy, TikTok strategy)
- Pricing Strategy Analysis ($2.99 premium positioning)
- Screenshots & Preview Video Best Practices (conversion optimization)
- Community Building for Indie Games (Discord, true fans math)
- Reviews & Ratings Strategy (when to prompt, how to respond)
- Getting Featured by Apple (submission process, what they look for)
- Localization Strategy (language tiers, what to localize)
- Case Studies: What Worked (Vampire Survivors, Slay the Spire, Stardew, Monument Valley, Hades)

**Critical Frameworks:**
- App Store metadata optimization (30-char title, 30-char subtitle, 100-char keywords)
- Launch week calendar (day-by-day action items)
- Screenshot narrative structure (10 screenshots with specific purposes)
- Marketing channel priority matrix (ASO #1, TikTok #2, Reddit #3)
- Community flywheel (great game → happy players → reviews → ranking → better game)
- Regional pricing strategy

---

## File Structure

This package includes:

```
/Game-Dev-Team-Knowledge/
  README.md (this file)
  /knowledge-base/
    spock-game-director.md
    geordi-game-architect.md
    scotty-game-developer.md
    worf-game-balance.md
    vic-game-audio.md
    quark-game-marketing.md
  /templates/
    game-design-document-template.md
    production-milestone-template.md
    qa-test-plan-template.md
    marketing-calendar-template.md
  /examples/
    bsp-dungeon-generator.gd
    state-machine.gd
    health-component.gd
    music-manager.gd
  /references/
    glossary.md
    recommended-reading.md
    tool-recommendations.md
```

---

## Next Steps

1. **Read Spock first** - Get the big picture of game development methodology
2. **Skim all sections** - Understand what knowledge exists where
3. **Deep dive as needed** - Reference specific sections when you hit those problems
4. **Adapt to your project** - This is a framework, not a prescription
5. **Build your own knowledge** - Add to this as you learn

---

## Credits

This knowledge base was compiled from Starfleet Crew documentation created during the development of **Oasis** by **Forge&Code**.

**Compiled by:** Cortana, First Officer
**Date:** December 2024
**For:** Ryan and Joel (and future indie game developers)

---

## License & Usage

This knowledge base is shared freely for educational and development use. If you use significant portions:
- Credit Forge&Code and the Starfleet Crew methodology
- Share your own learnings back to the community
- Remember: knowledge compounds when shared

**The best way to thank us:** Make a great game. Then help the next developer do the same.

---

*"The needs of the many outweigh the needs of the few. Or the one. Unless that one is making something genuinely great — then help them."*

*-- Spock (with Cortana's edit)*
