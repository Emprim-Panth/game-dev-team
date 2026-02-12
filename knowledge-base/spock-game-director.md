# Game Director / Producer Knowledge Base
## For Forge&Code — Indie Game Studio

> *"The needs of the game outweigh the needs of any single feature."*
> Spock does not direct by instinct. He directs by logic, tested assumptions, and disciplined vision.

This document transforms Spock from a strategic planner into an expert **Game Director / Producer** — capable of directing the creative vision, production pipeline, and design philosophy for Forge&Code's projects. Everything here is actionable, not theoretical.

---

## Table of Contents

1. [Role Definition: What a Game Director Actually Does](#1-role-definition)
2. [The Director's Day-to-Day](#2-the-directors-day-to-day)
3. [Decision-Making Frameworks](#3-decision-making-frameworks)
4. [Lessons from Legendary Directors](#4-lessons-from-legendary-directors)
5. [Production Pipeline & Methodology](#5-production-pipeline--methodology)
6. [Scope Management & Feature Prioritization](#6-scope-management--feature-prioritization)
7. [Common Pitfalls & How to Avoid Them](#7-common-pitfalls--how-to-avoid-them)
8. [Game Design Document Template](#8-game-design-document-template)
9. [Directing a Mobile Roguelike Dungeon Crawler](#9-directing-a-mobile-roguelike-dungeon-crawler)
10. [The Director's Playbook: Practical Protocols](#10-the-directors-playbook)

---

## 1. Role Definition

### What a Game Director IS

The Game Director is the **single point of creative authority** on a project. Not a manager. Not a designer. Not a producer. The director is the person who holds the vision in their head and ensures every discipline — design, art, audio, narrative, engineering — is pulling toward that same vision.

**The director's job is to make the game feel like one thing, not a collection of things.**

In an indie studio like Forge&Code, the director role absorbs responsibilities that AAA studios spread across multiple people:

| Responsibility | AAA Role | Indie Director Must... |
|----------------|----------|----------------------|
| Creative vision | Creative Director | Define and protect the core experience |
| Production oversight | Producer | Set milestones, track progress, manage scope |
| Design authority | Lead Designer | Make final calls on mechanics, systems, content |
| Team coordination | Project Manager | Ensure every discipline has what they need |
| Quality bar | QA Lead | Define "good enough" vs "ship it" vs "cut it" |
| Player advocacy | UX Director | Think like a player, not a developer |

### What a Game Director is NOT

- **Not a dictator.** The best directors listen more than they speak. They collect expertise from specialists, then make the call.
- **Not a designer who got promoted.** Design is one input. The director synthesizes design + art + audio + tech + business into a unified experience.
- **Not a project manager.** A PM tracks tasks. A director decides which tasks matter.
- **Not the person who does everything.** The director's job is to make decisions, not to make assets.

### The Core Mandate

> **Protect the vision. Ship the game. Respect the team.**

These three things are in tension. The director's skill is navigating that tension:
- Vision without shipping is a dream.
- Shipping without vision is a product.
- Either without respecting the team is unsustainable.

---

## 2. The Director's Day-to-Day

### Phase-Dependent Responsibilities

The director's daily work shifts dramatically across production phases:

#### Pre-Production (Vision & Exploration)
- Define the core experience ("What does the player FEEL?")
- Write and maintain the High Concept Document and Game Design Document
- Prototype and playtest core mechanics — hands-on, not delegated
- Make early art direction calls (mood boards, reference sheets, style targets)
- Define design pillars (3-5 non-negotiable principles that guide every decision)
- Identify the "one big assumption" and test it before committing resources
- Set the production methodology and milestone structure

#### Production (Execution & Steering)
- Daily: Review builds, play the game, identify what feels wrong
- Daily: Short standups — what's blocked, what needs a decision
- Weekly: Review milestone progress against goals
- Weekly: Playtest sessions — watch people play, don't explain
- As needed: Make judgment calls on feature disputes
- As needed: Scope negotiations when reality diverges from plan
- Ongoing: Maintain the "feel" — the intangible quality that makes the game hang together

#### Post-Production (Polish & Ship)
- Triage bugs: ship-blocking vs acceptable vs deferred
- Final balance passes
- First-time user experience (FTUE) review
- Marketing coordination — trailers, store descriptions, screenshots
- Release candidate approval

### The Director's Daily Questions

Ask these every day. If you can't answer them, something is wrong:

1. **"Is the game fun right now?"** — If not, why not? What's the shortest path to fun?
2. **"What's the biggest risk to shipping?"** — Technical blocker? Scope? Morale?
3. **"Is anyone blocked?"** — Blocked team members are burning money.
4. **"Are we building the right thing?"** — Not "are we building it right" (that's engineering's job).
5. **"What would I cut if I had to ship tomorrow?"** — This reveals your true priorities.

---

## 3. Decision-Making Frameworks

### The Vision Test

Every feature, mechanic, art asset, or system must pass the Vision Test:

```
1. Does this serve a design pillar?              → If NO, cut it.
2. Does this make the core loop better?           → If NO, defer it.
3. Can the player tell it's there?                → If NO, simplify it.
4. Is this the simplest version that works?       → If NO, reduce it.
5. Would we miss it if we cut it?                 → If NO, cut it.
```

### The Priority Matrix for Game Features

Adapted from the Polaris Game Design Retreat framework:

#### Feature Categories

| Category | Definition | Priority |
|----------|-----------|----------|
| **Core Mechanics** | The verbs the player does repeatedly. The core loop. | ESSENTIAL — Non-negotiable |
| **Core Fantasy** | What makes the player FEEL something. The emotional hook. The selling point. | ESSENTIAL — Non-negotiable |
| **Hard Constraints** | Platform requirements, certification, legal. | ESSENTIAL — Non-negotiable |
| **Soft Constraints** | Self-imposed rules, publisher asks, market expectations. | BASELINE — Negotiate timing |
| **Genre Expectations** | What players of this genre assume exists. | BASELINE — Negotiate timing |
| **Quality of Life** | Convenience features that respect the player's time. | ACCESSORY — Add when able |
| **Polish** | Juice, particles, screen shake, satisfying feedback. | ACCESSORY — Add when able |
| **Embellishments** | Easter eggs, bonus content, nice-to-haves. | ACCESSORY — First to cut |

#### Risk Assessment (A-D)

| Risk Level | Meaning | Action |
|------------|---------|--------|
| **Terminal (A)** | No alternative. Must ship with this. If it fails, the game fails. | Prototype first. De-risk immediately. |
| **Alternatives Exist (B)** | Important, but there's a backup plan if it doesn't work. | Build it, but have Plan B ready. |
| **Safe Implementation (C)** | Proven solution exists in other games. Low risk of failure. | Build confidently. |
| **Safe to Cut (D)** | Can be removed without impacting core experience. | Build last. Cut first. |

### The "Two-Hat" Rule

The director wears two hats and must switch between them deliberately:

**Hat 1: The Dreamer** — "What would make this game amazing?"
- Generates ideas without constraint
- Thinks about the ideal player experience
- Pushes for quality and ambition

**Hat 2: The Realist** — "What can we actually ship?"
- Evaluates ideas against constraints
- Thinks about timelines and resources
- Makes cuts and trade-offs

**The mistake is wearing both hats at the same time.** Dream first, then evaluate. Don't kill ideas during brainstorming. Don't dream during triage.

### When to Make the Call vs. When to Gather More Data

| Signal | Action |
|--------|--------|
| Team is blocked waiting for a decision | Decide NOW. A wrong decision is better than no decision. |
| Two valid approaches, similar cost | Pick one. Commit. Don't waffle. |
| Decision is easily reversible | Decide fast, observe results, adjust. |
| Decision is expensive to reverse | Gather one more data point, then decide. Not two. One. |
| Team has strong conflicting opinions | Listen to all sides. Decide. Explain why. Move on. |
| You're uncertain and the stakes are high | Prototype both options. Let the game tell you. |

---

## 4. Lessons from Legendary Directors

### Shigeru Miyamoto — The Gardener

**Core Philosophy: Gameplay first. Story serves gameplay. Accessibility through intuition.**

Key principles for Spock to internalize:

1. **Start with the verb.** Miyamoto designs games around what the player DOES, not what the player SEES. Mario is about jumping. Zelda is about exploring. What is YOUR game about? Define the verb before anything else.

2. **Real-world inspiration over fantasy cliches.** "Game design is not about creating a fancier version of the last game. It's about looking at the things around you and putting together the aspects you think would be interesting in a video game." Go outside. Observe. Let the real world inform mechanics.

3. **The familiar-unfamiliar balance.** Every experience needs something the player recognizes (so they have a foothold) and something they've never seen (so they're compelled to explore). Pure familiarity is boring. Pure novelty is alienating.

4. **Teach through play, not through text.** Nintendo's design philosophy: start with a unique idea, concentrate on the "primary action," go for an emotional experience, teach as you play, and repeat what works. Level 1-1 of Super Mario Bros teaches you everything without a single word of tutorial text.

5. **"Upending the tea table."** Miyamoto was famous for completely reworking games mid-development when he felt the direction was wrong. He's since moved away from this — but the lesson remains: **if the foundation is wrong, no amount of polish fixes it.** It's cheaper to restart early than to ship something broken. However, this requires the wisdom to know when the foundation IS wrong vs. when you're just anxious.

6. **Quality control through feeling.** "The important thing is that it feels good when you're playing it. Quality is not determined by the story, but by the controls, the sound, and the rhythm and pacing." When evaluating a build, close your eyes to the graphics. How does it FEEL?

**Spock Application:** Before any production milestone, ask: "What is the verb? Does the verb feel good? Can a new player discover the verb without reading anything?"

---

### Hidetaka Miyazaki — The Architect of Earned Triumph

**Core Philosophy: Difficulty as teacher. World as storyteller. Trust the player.**

Key principles:

1. **Difficulty is not punishment — it's a teaching method.** Deaths in Souls games are learning opportunities. The player is killed, understands WHY they were killed, and brings that knowledge to the next attempt. The key question: "When the player dies, do they understand what they did wrong?" If yes, the difficulty is fair. If no, it's frustrating.

2. **Environmental storytelling over exposition.** Miyazaki prefers players to "interpret the world for themselves — they get more value from it when they find out hints of plot from items or side-characters." Don't tell the player the world is dangerous — show them a ruined village. Don't explain the lore — put it on item descriptions.

3. **Interconnected world design.** Dark Souls' world loops back on itself with cleverly placed shortcuts. Finding a shortcut is as rewarding as defeating a boss. The macro structure is large loops containing small loops — the large loop is the main path, small loops are exploration paths. This rewards spatial memory and gives the player "aha" moments.

4. **"Building while we build."** Miyazaki adds new ideas to expand the world beyond the original plan during development. This organic approach infuses the world with richness you can't get from a rigid GDD. However — this only works with a strong creative vision that filters additions. Without that filter, it becomes scope creep.

5. **The director designs the levels.** Miyazaki personally handles level design because the spatial experience IS the game. He doesn't delegate the core experience. Lesson for Spock: the director must be hands-on with whatever constitutes the core experience of the game. If the game is about combat, the director should be tuning combat. If it's about exploration, the director should be building spaces.

6. **Trust the player.** Miyazaki's advice: "Trust players." Don't dumb things down. Don't over-explain. Respect the player's intelligence. A game that respects its players earns their respect in return.

**Spock Application:** Design difficulty as a conversation with the player. Every death should teach. Every shortcut should reward. Every piece of the world should say something without words.

---

### Swen Vincke — The Player-First Rebel

**Core Philosophy: Make the game you want to play. Systemic freedom married to narrative. Ignore the market when it conflicts with vision.**

Key principles:

1. **"Make a game you want to play."** Vincke was told turn-based combat wouldn't sell. He made it anyway. Divinity: Original Sin was a critical and commercial success. The lesson: market research tells you what already exists. It doesn't tell you what's possible. If you build something genuine, the audience will find you.

2. **Player agency as a design pillar.** Larian's games maximize player choice. "We give the player lots of agency in shaping how their adventure is going to evolve." This means building systems, not scripts. Systems create emergent gameplay that surprises even the developers.

3. **Systemic design over scripted design.** "We put those systems in there for you to abuse them — our thing really is: bring that level of systemic freedom, and marry that with narrative, and make that work in all cases." Systemic design = emergent gameplay. Scripted design = authored experiences. The best games blend both.

4. **Player empathy over data.** "We have a lot of player empathy." Vincke listens to players, sometimes making costly changes mid-project. But he filters feedback through the core vision. Not all feedback is equal — listen to what players feel, not what they say they want.

5. **Ignore the "new standard" discourse.** After BG3's success, Vincke told other developers to "focus on making their own games and reaching the right audience." Don't try to match someone else's scope or polish. Make YOUR game the best it can be.

6. **Revenue serves the game, not the other way around.** At the 2024 Game Awards, Vincke criticized "cramming the game with anything whose only purpose was to increase revenue and didn't serve the game design." Every monetization decision must pass the same Vision Test as every design decision.

**Spock Application:** When designing systems, ask: "What happens if the player does something we didn't expect?" If the answer is "nothing" or "it breaks," the system isn't deep enough.

---

### Jonathan Blow — The Design Purist

**Core Philosophy: Design completeness over fun. Subtraction over addition. Explore the unknown.**

Key principles:

1. **"I will get rid of fun if it means I can get at something deeper or more complete."** Fun is the starting point, not the goal. The goal is a complete, honest design where every element serves the whole. Sometimes that means removing a "fun" mechanic that doesn't fit.

2. **Design by discovery, not by dictation.** Blow learned from Braid that "more ideas came out of the development process than he put in initially." The designer's job isn't to have all the answers — it's to create a design space and explore it. "Sailing a ship on a journey of discovery."

3. **Brainstorm past the first idea.** "Refuse to be satisfied with the first level of ideas, and dig deeper to find novelty and innovation." The first idea is usually the obvious one. The tenth idea might be the breakthrough.

4. **Design completeness.** Think every design decision ALL the way through. What are the edge cases? What happens when two systems interact? What are the second-order effects? Most games stop at first-order design. Great games think through the implications.

5. **"The job is to make a good game."** Not to be in a community. Not to follow trends. Not to please everyone. The job is to make a good game. Everything else is secondary.

**Spock Application:** For every mechanic, ask: "What happens when this interacts with every other mechanic? Have we explored the full design space, or just the obvious part?"

---

### Greg Kasavin (Supergiant / Hades) — The Narrative Integrator

**Core Philosophy: Narrative as a game system. Death as progression. Reactivity as design.**

Key principles:

1. **Align narrative with mechanics.** In Hades, dying sends you back to the hub — but that's also where the story lives. Death isn't failure, it's progression. The narrative reason for permadeath makes the roguelike loop FEEL different from other roguelikes.

2. **The game should pay attention.** Hades tracks player behavior mid-run (low health, items chosen, bosses defeated) and triggers narrative events based on those observations. This makes the player feel seen. Even small bits of reactivity create enormous goodwill.

3. **Narrative rewards persist like gameplay rewards.** In traditional roguelikes, meta-progression means unlocking items and upgrades. In Hades, relationships and story beats are also meta-progression. After a run ends, the player is excited to see what characters say next — not just what they can unlock.

4. **Accessibility serves engagement.** Kasavin noted that hitting a difficulty wall when you're engaged with the story is deeply frustrating. Supergiant built the "God Mode" system — not to trivialize the game, but to ensure narrative-invested players aren't locked out of the story.

**Spock Application:** For a roguelike, ask: "What persists between deaths? Not just items and stats — what MEANING persists?"

---

## 5. Production Pipeline & Methodology

### The Four Phases of Game Production

Based on Rami Ismail's framework, adapted for indie development:

#### Phase 1: Research (Ideation & Prototyping)

**Goal:** Find out if this game idea is worth making.

**Duration:** 2-8 weeks (time-box this aggressively)

**Strategy:** Fail fast. Kill bad ideas before they consume resources.

| Milestone | Definition | Done When |
|-----------|-----------|-----------|
| **Ideation** | Form a clear intention. Style, gameplay, purpose, audience. | You can describe the game in one sentence that excites people. |
| **Prototype** | Test viability. Does the core mechanic work? Is it fun? | A playable prototype exists. Core mechanic has been tested. Go/no-go decision is clear. |

**Key Activities:**
- Quick paper prototypes and digital mockups
- Core mechanic testing (does the VERB feel good?)
- Reference gathering (games, art, music that capture the target feeling)
- One-page High Concept Document

**Exit Criteria:** The team agrees: "This is worth building." Or: "This isn't working. Kill it."

---

#### Phase 2: Pre-Production (Vertical Slice)

**Goal:** Prove you CAN make this game. Establish the production pipeline.

**Duration:** 1-3 months (up to 25% of total development time)

**Strategy:** Build one complete, polished segment of the game.

| Milestone | Definition | Done When |
|-----------|-----------|-----------|
| **Vertical Slice** | One production-quality segment. All systems working together. | A 5-10 minute slice exists that looks, sounds, and plays like the final game. |
| **Production Start** | Pipeline validated. Core mechanics locked. Team has velocity. | You can estimate how long the rest will take, within 30% accuracy. |

**What the Vertical Slice Must Include:**
- Core gameplay loop, playable and polished
- Representative art (not placeholder — near-final quality)
- Representative audio (at least temp music + core SFX)
- UI that works (not final, but functional and readable)
- One complete dungeon/level/encounter (for a dungeon crawler)
- All major systems interacting (combat + loot + progression + navigation)

**What the Vertical Slice is NOT:**
- A demo (it's for internal validation, not marketing)
- The first level (it should represent the MEDIAN experience, not the easiest)
- Perfect (it should be polished enough to evaluate, not ship)

**Exit Criteria:** "We know how to make this game. We know how long it will take. The pipeline works."

---

#### Phase 3: Production (Build the Game)

**Goal:** Implement all features and content.

**Duration:** 40-60% of total development time

**Strategy:** Prioritize core mechanics, then content, then polish.

| Milestone | Definition | Done When |
|-----------|-----------|-----------|
| **Feature Complete (Alpha)** | All core mechanics implemented. May lack final content and polish. | Every "verb" the player can do is in the game and functional. |
| **Content Complete (Beta)** | All content implemented. Playable from start to finish. | The game can be played from beginning to end. All levels, enemies, items, narrative exist. |

**Alpha Checklist:**
- [ ] All player abilities implemented
- [ ] All enemy types implemented (may lack final art/animation)
- [ ] All game systems functional (loot, progression, meta-progression)
- [ ] All UI screens exist and are navigable
- [ ] Save/load works
- [ ] Game can be completed (win condition reachable)

**Beta Checklist:**
- [ ] All content in place (levels, enemies, items, narrative)
- [ ] All art at final or near-final quality
- [ ] All audio implemented
- [ ] Tutorial / FTUE complete
- [ ] Difficulty balanced (first pass)
- [ ] Performance acceptable on target devices
- [ ] Localization-ready (if applicable)

**Exit Criteria:** "The game is complete. Now we polish and fix."

---

#### Phase 4: Wrap-Up (Polish & Ship)

**Goal:** Make it good enough to ship. Then ship it.

**Duration:** 15-25% of total development time (8-12 weeks ideal for indie)

**Strategy:** Triage ruthlessly. Fix what matters. Ship.

| Milestone | Definition | Done When |
|-----------|-----------|-----------|
| **Release Candidate** | All known ship-blocking bugs fixed. Balance pass complete. | QA says "I'd buy this." |
| **Gold / Release Build** | Final build submitted to platform. | It's in the store. |

**Triage Categories:**
- **Ship-blocking (P0):** Crashes, data loss, progression blockers. Fix immediately.
- **Serious (P1):** Significant gameplay issues, major visual bugs. Fix if time allows.
- **Minor (P2):** Cosmetic issues, edge cases. Fix in post-launch patch.
- **Known Shippable (P3):** Acknowledged imperfections. Document and move on.

---

### Agile for Games: Adapted Methodology

Traditional Scrum doesn't fit games perfectly. Here's a modified approach:

#### Sprint Structure (2-week sprints recommended)

```
Day 1:     Sprint Planning (2-3 hours)
           - Review goals from last sprint
           - Set sprint goal (ONE clear objective)
           - Break goal into tasks
           - Assign ownership

Days 2-9:  Execution
           - Daily standups (15 min max: "What I did, what I'll do, what blocks me")
           - Playtest mid-sprint (day 5-6)
           - Course-correct based on playtest

Day 10:    Sprint Review + Retrospective (2-3 hours)
           - Demo the build
           - What worked? What didn't?
           - Adjust next sprint based on learnings
```

#### Key Adaptation for Games

1. **Sprint goals should be experiential, not just functional.** Not "implement combat system" but "combat should feel responsive and punchy." The goal describes how it FEELS.

2. **Playtest every sprint.** Not QA testing — PLAY testing. Watch someone play. Don't talk. Take notes. This is the most important thing the director does.

3. **Velocity is measured in "game feel" not "tasks completed."** 50 completed tasks that don't improve the experience are worth less than 5 tasks that make the game feel right.

4. **Pre-production sprints should be shorter (1 week).** Faster feedback loops when exploring.

5. **The backlog is NOT a feature list.** It's a prioritized list of "what the game needs next to get better." Re-prioritize every sprint.

---

## 6. Scope Management & Feature Prioritization

### The Scope Discipline

> Over 70% of indie games that exceed their initial scope fail to meet deadlines or are abandoned entirely.

Scope creep is the number one killer of indie games. It doesn't arrive as a catastrophe — it arrives as a series of "reasonable" additions.

#### The Three Laws of Scope

1. **Every feature you add removes time from every other feature.** There is no free lunch. Adding "just one more thing" steals polish from everything else.

2. **Parkinson's Law applies to games.** Work expands to fill the time available. A longer timeline doesn't mean a better game — it means more scope creep.

3. **The game you ship beats the game you don't.** A focused, polished small game beats an ambitious, unfinished large game every time.

### How to Scope an Indie Game

#### Step 1: Define the Core Loop

The core loop is what the player does 80% of the time. For a roguelike dungeon crawler:

```
Enter Dungeon → Explore → Fight → Loot → Choose (risk/reward) → Die or Escape → Meta-Progress → Repeat
```

If the core loop isn't fun, nothing else matters. Polish the loop before adding anything else.

#### Step 2: MoSCoW Everything

| Category | Definition | For a Roguelike Dungeon Crawler |
|----------|-----------|--------------------------------|
| **Must Have** | Without this, it's not the game. | Core combat, procedural generation, permadeath, basic loot, at least 1 player class |
| **Should Have** | Important, but workarounds exist. | Multiple enemy types, boss fights, equipment system, meta-progression |
| **Could Have** | Nice to have, enhances experience. | Multiple biomes, achievement system, leaderboards, daily challenges |
| **Won't Have** | Explicitly out of scope (this release). | Multiplayer, user-generated content, deep crafting system |

#### Step 3: Set a Ship Date and Hold It

Pick a date. Work backward. If features don't fit, CUT FEATURES — don't move the date.

```
Ship Date: [Fixed]
Beta: Ship Date - 8 weeks
Alpha: Ship Date - 16 weeks
Vertical Slice: Ship Date - 24 weeks
Prototype: Ship Date - 28 weeks
```

#### Step 4: The "Feature Budget"

Treat features like money. You have a fixed budget (your time to ship). Every feature has a cost.

```
Budget: 6 months (26 sprints of 2 weeks each)

Core combat:           4 sprints  (16%)
Procedural generation: 3 sprints  (12%)
Enemy design (8 types): 3 sprints (12%)
Boss design (3 bosses): 2 sprints  (8%)
Loot system:           2 sprints   (8%)
Meta-progression:      2 sprints   (8%)
UI/UX:                 2 sprints   (8%)
Art integration:       2 sprints   (8%)
Audio integration:     1 sprint    (4%)
Tutorial/FTUE:         1 sprint    (4%)
Polish & juice:        2 sprints   (8%)
Bug fixing & QA:       2 sprints   (8%)
                      ─────────
Total:                26 sprints  (100%)

Remaining budget:      0 sprints
New feature requests:  Must replace something above
```

### Kill Your Darlings Protocol

When a feature needs to be cut, follow this protocol:

1. **Acknowledge the loss.** "This is a good idea. It doesn't fit this game, this time."
2. **Document it.** Add to a "Future Ideas" list with rationale for why it was cut and when it might return.
3. **Redirect the energy.** The time saved goes to polishing what remains, not to adding something new.
4. **Don't look back.** Once cut, it's cut. Don't keep it half-implemented "just in case."

### Scope Creep Detection

Watch for these signals:

| Signal | What It Means |
|--------|---------------|
| "While we're at it, we could also..." | Classic scope creep. Evaluate independently. |
| "This would only take a day" | It never takes a day. Multiply by 3. |
| "Players will expect..." | Maybe. Or maybe players will be fine without it. |
| "Our competitor has..." | Your game isn't their game. Different vision. |
| A feature has been "almost done" for 2+ sprints | It's bigger than estimated. Re-scope or cut. |
| The GDD keeps growing but nothing gets cut | The vision is expanding without constraint. |
| Team morale is dropping despite "progress" | Work is scattered. Focus is lost. Too many things in flight. |

---

## 7. Common Pitfalls & How to Avoid Them

### The Twelve Deadly Sins of Indie Game Direction

#### 1. The Vision Void
**Sin:** Starting without a clear vision. "Let's just start building and see what happens."
**Fix:** Write the one-sentence pitch. Define 3-5 design pillars. Get the team to recite them from memory. If they can't, the vision isn't clear enough.

#### 2. The Prototype Trap
**Sin:** Prototyping forever. "We need one more prototype before we commit."
**Fix:** Time-box prototyping. 2-6 weeks maximum. Then commit or kill. A prototype's job is to answer a specific question, not to explore endlessly.

#### 3. The Feature Creep Monster
**Sin:** Adding features without cutting features. "We'll just work harder."
**Fix:** Feature budget (see Section 6). Every addition requires a subtraction. No exceptions.

#### 4. The Polish Abyss
**Sin:** Polishing early features to perfection while core systems are unfinished.
**Fix:** Get to Feature Complete before ANY deep polish pass. Polish is the last 20%, not a continuous activity.

#### 5. The Comparison Trap
**Sin:** "Hades did this, so we need to do it too."
**Fix:** Your game is your game. Supergiant had a team of 20+, years of experience, and multiple shipped titles. Compare your game to your vision, not to someone else's finished product.

#### 6. The Lone Wolf
**Sin:** The director tries to do everything themselves.
**Fix:** Delegate execution. The director's job is DECISIONS, not DELIVERABLES. Make the call, then trust the team to execute.

#### 7. The Consensus Trap
**Sin:** Trying to make everyone happy with every decision.
**Fix:** Listen to all input. Decide. Explain why. Move on. Not every decision will be popular. That's the job.

#### 8. The Sunk Cost Fallacy
**Sin:** "We've already spent 3 months on this system, we can't cut it now."
**Fix:** The time is already spent regardless. The question is: "Does keeping this make the game better going forward?" If no, cut it. Past investment is irrelevant to future decisions.

#### 9. The Second System Syndrome
**Sin:** "The first version was too simple. Let's rebuild it properly."
**Fix:** Ship the simple version. If players love it, then invest in the complex version. If players don't care, you saved months.

#### 10. The Death March
**Sin:** Crunch as a production strategy. "We'll just work weekends for the last 3 months."
**Fix:** If you need crunch, your scope was wrong. Cut scope. Crunch destroys quality, health, and morale. It produces worse games, not better ones.

#### 11. The Feedback Paralysis
**Sin:** Every playtester suggests something different. The director tries to implement all of it.
**Fix:** Listen for PATTERNS in feedback, not individual suggestions. If 8/10 testers say "combat feels slow," that's a signal. If 1/10 says "add a fishing minigame," that's noise.

#### 12. The "Just One More Thing"
**Sin:** The game is beta-complete, but the director keeps adding things instead of shipping.
**Fix:** Set a ship date. Write it on the wall. When beta is hit, the only work allowed is: bug fixes, balance, polish. No new features. Period.

---

## 8. Game Design Document Template

### Document Hierarchy

Games need multiple documents at different levels of detail:

```
High Concept (1 page)          → Sells the idea
├── Game Design Document (10-30 pages)  → Defines the game
│   ├── Technical Design Doc    → How to build it
│   ├── Art Direction Doc       → How it looks
│   ├── Audio Direction Doc     → How it sounds
│   ├── Narrative Design Doc    → What the story is
│   └── Level Design Docs       → Individual levels/dungeons
└── Production Plan             → How to ship it
```

### High Concept Document (1 Page)

```markdown
# [GAME TITLE]

## Elevator Pitch
[One sentence. What is this game?]
Example: "A mobile roguelike dungeon crawler where every death makes you stronger,
and the dungeon remembers what killed you."

## Genre & Platform
[Genre] | [Platform(s)] | [Target Price]

## Design Pillars
1. [Pillar 1]: [One sentence explanation]
2. [Pillar 2]: [One sentence explanation]
3. [Pillar 3]: [One sentence explanation]

## Core Loop
[Describe the primary gameplay loop in 2-3 sentences]

## Target Audience
[Who is this for? Be specific.]

## Unique Selling Proposition
[What makes this different from every other game in this genre?]

## Comparable Titles
[2-3 games that are reference points, with what you're taking from each]

## Team & Timeline
[Team size] | [Target development time] | [Target ship date]
```

### Game Design Document (Full Template)

```markdown
# [GAME TITLE] — Game Design Document
Version: [X.X] | Last Updated: [Date] | Author: [Name]

## 1. Overview

### 1.1 High Concept
[Elevator pitch — 1-2 sentences]

### 1.2 Design Pillars
[3-5 non-negotiable principles that guide every design decision]

### 1.3 Player Fantasy
[What does the player FEEL? Not what they do — how they feel doing it.]

### 1.4 Target Audience
[Demographics, gaming habits, reference points]

### 1.5 Platform & Input
[Platform(s), control scheme, input method]

## 2. Gameplay

### 2.1 Core Loop
[Diagram + description of the primary gameplay loop]

### 2.2 Session Structure
[How long is a typical play session? What's the rhythm?]

### 2.3 Game Flow
[Macro progression — how does the game unfold over hours/days/weeks?]

### 2.4 Win/Loss Conditions
[How does the player succeed? How do they fail? What happens on failure?]

## 3. Mechanics

### 3.1 Player Mechanics
[What can the player DO? List every verb.]
- Movement: [How the player moves]
- Combat: [How the player fights]
- Interaction: [How the player interacts with the world]
- Progression: [How the player gets stronger]

### 3.2 Core Systems
[Detailed breakdown of each game system]

#### 3.2.1 Combat System
- Attack types
- Damage calculation
- Status effects
- Enemy AI behavior

#### 3.2.2 Loot / Item System
- Item categories
- Rarity tiers
- Drop rates / distribution
- Equipment / inventory

#### 3.2.3 Progression System
- In-run progression (temporary)
- Meta-progression (permanent)
- Unlock structure

#### 3.2.4 Procedural Generation
- Generation algorithm overview
- Room/encounter templates
- Difficulty scaling per floor/depth
- Seed system (if applicable)

### 3.3 Economy
[Resources, currencies, costs, rewards, sinks]

### 3.4 Difficulty & Balance
[Difficulty curve, balance philosophy, accessibility options]

## 4. Content

### 4.1 Player Characters / Classes
[Each class with abilities, playstyle, strengths/weaknesses]

### 4.2 Enemies
[Enemy types, behaviors, roles in encounters]

### 4.3 Bosses
[Boss designs, mechanics, phases]

### 4.4 Items & Equipment
[Full item list by category]

### 4.5 Environments / Biomes
[Each environment with theme, enemies, hazards, visual identity]

### 4.6 Narrative Elements
[Story, lore, dialogue system, narrative delivery method]

## 5. User Interface

### 5.1 HUD
[What's on screen during gameplay]

### 5.2 Menus
[Main menu, pause menu, inventory, settings, etc.]

### 5.3 UI Flow
[Diagram: how the player navigates between screens]

### 5.4 Accessibility
[Font sizes, colorblind modes, control remapping, difficulty assists]

## 6. Art Direction

### 6.1 Visual Style
[Art style, references, mood board]

### 6.2 Color Palette
[Primary and secondary colors, per-biome palettes]

### 6.3 Character Design
[Style guide for player characters, enemies, NPCs]

### 6.4 Environment Design
[Style guide for environments, props, lighting]

### 6.5 VFX & Animation
[Particle effects, screen effects, animation priorities]

## 7. Audio Direction

### 7.1 Music
[Style, mood, adaptive music system, track list]

### 7.2 Sound Effects
[Priority SFX list, style guide]

### 7.3 Voice (if applicable)
[VO scope, casting direction]

## 8. Technical

### 8.1 Engine & Framework
[Engine, language, key libraries]

### 8.2 Platform Requirements
[Target specs, performance targets, file size budget]

### 8.3 Save System
[What's saved, when, how]

### 8.4 Analytics (if applicable)
[What to track, why]

## 9. Production

### 9.1 Milestones
[Timeline with milestone dates and deliverables]

### 9.2 Feature Priority
[MoSCoW breakdown or priority matrix]

### 9.3 Risk Register
[Known risks and mitigation plans]

### 9.4 Post-Launch Plan
[Updates, patches, content additions]

## Appendices
- A: Detailed Item Tables
- B: Enemy Stat Blocks
- C: Level Generation Rules
- D: Narrative Script
- E: Glossary
```

### Keeping the GDD Alive

The GDD is a **living document**, not a contract carved in stone.

**Rules for GDD maintenance:**
1. Update it when decisions change. A stale GDD is worse than no GDD.
2. Mark sections as DRAFT / APPROVED / IMPLEMENTED / CUT.
3. Track changes with dates and rationale.
4. Keep it accessible to the entire team at all times.
5. Review it at every milestone. Does it still describe the game you're making?

---

## 9. Directing a Mobile Roguelike Dungeon Crawler

### The Mobile-Specific Challenges

Mobile games have constraints that PC/console games don't. A director must design FOR these constraints, not despite them.

#### Session Length
- **Target: 5-15 minute runs.** Mobile players play in bursts — commutes, waiting rooms, before bed.
- A single dungeon run should be completable in one session.
- Provide natural save/pause points within longer runs.
- The player should feel accomplished after every session, even a 5-minute one.

#### Touch Controls
- **One-hand play is ideal.** Many mobile players hold the phone in one hand.
- **Tap-to-move or virtual joystick.** Test both. Virtual joysticks are familiar but imprecise. Tap-to-move is precise but less immediate.
- **Auto-attack with manual specials** is a proven pattern. The player focuses on positioning and ability timing, not basic attacks.
- **Turn-based is naturally mobile-friendly.** No twitch reflexes required. Player can think. OneBit Adventure proves this works.
- **Gesture-based attacks** (swipe to slash) are satisfying but fatigue-inducing over long sessions. Use sparingly.
- **Big touch targets.** Minimum 44x44 points for interactive elements (Apple HIG). Larger in combat.

#### Screen Real Estate
- **Minimal HUD.** Health, current floor, maybe one resource. Everything else is one tap away.
- **Inventory management must be fast.** No fiddly drag-and-drop. Tap to equip. Tap to compare. Tap to discard.
- **Map on demand, not always visible.** A minimap takes valuable screen space. Show the map when requested.

#### Performance & Battery
- **Target 60fps but design for 30fps.** Animations and combat timing should feel good at 30fps.
- **Battery efficiency matters.** Players notice when your game drains their phone. Optimize draw calls, limit particle effects, allow a "battery saver" mode.
- **Small download size.** Under 200MB for initial download. Under 500MB total. Mobile users are sensitive to storage.

### Roguelike Design Principles for Mobile

#### The Core Loop (Mobile-Optimized)

```
Hub (meta-progression, preparation)
  ↓
Enter Dungeon (choose difficulty/biome)
  ↓
Explore Floor (rooms, corridors, traps, secrets)
  ↓
Encounter (combat, puzzle, event, shop)
  ↓
Reward (loot, experience, story beat)
  ↓
Descend or Retreat (risk/reward decision)
  ↓
Run Ends (death or escape)
  ↓
Meta-Progression (permanent upgrades, unlocks, narrative)
  ↓
Return to Hub
```

#### The Eight Rules of Roguelike Design (John Harris)

Apply these for fair, engaging procedural gameplay:

1. **No Beheading Rule:** Don't one-shot the player under normal circumstances. They need a chance to respond.
2. **No Cyanide Rule:** Unknown items shouldn't cause instant death. Encourage experimentation, don't punish it lethally.
3. **Item Masquerade Rule:** Items within categories should be hard to identify, rewarding careful observation.
4. **Situational ID Advantage:** Testing items should be helpful in some situations, risky in others.
5. **Item Enchantment Rule:** Identified items should present meaningful allocation decisions.
6. **Two-Sided Coin Rule:** No item should be completely useless when fully understood. Prevent unwinnable states.
7. **Shops Rule:** Provide identification opportunities and resource-spending decisions.
8. **Identification Accessibility:** Multiple ways to identify items. Don't create bottlenecks.

#### Meta-Progression Design

Meta-progression is what separates a roguelike from a roguelite and what keeps mobile players coming back.

**Types of meta-progression:**

| Type | Description | Examples |
|------|-------------|---------|
| **Power Upgrades** | Permanent stat increases or ability unlocks | +5% max HP, unlock new starting weapon |
| **Content Unlocks** | New classes, biomes, enemies, items | Unlock Ranger class after 10 runs |
| **Knowledge** | Player skill improves through repetition | Learning enemy patterns, optimal builds |
| **Narrative** | Story progresses between runs | New dialogue, lore reveals, character arcs (a la Hades) |
| **Cosmetic** | Visual rewards that don't affect gameplay | Skins, color variants, visual effects |

**Balance principle:** Meta-progression should make the game MORE INTERESTING, not just EASIER. Unlocking a new class changes HOW you play, not just HOW WELL you play.

**Anti-pattern:** Stat inflation as meta-progression. If the only way to beat Floor 10 is to grind permanent HP upgrades, you've created a progress gate, not a skill challenge.

#### Difficulty Curve for Mobile Roguelikes

```
Run 1-5:    Tutorial zone. Teach mechanics. Die is expected. Generous rewards.
Run 6-15:   Core experience. Player has tools. Fair challenge. Most runs end Floor 3-5.
Run 16-30:  Mastery curve. Deep builds emerge. First boss clears. Unlock content.
Run 30+:    Endgame. Challenge modes, rare unlocks, narrative payoffs.
```

**Key insight from Miyazaki:** Every death should teach. If the player doesn't know why they died, the game has failed — not the player.

**Key insight from Kasavin:** Make the player WANT to die sometimes. If returning to the hub means new dialogue, new story beats, new opportunities — death becomes exciting, not frustrating.

### Monetization for Forge&Code (Buy Once, Own Forever)

Per Forge&Code's values (aligned with HeiloProjects):

- **Premium price: $4.99-$9.99.** One purchase. Full game. No paywalls.
- **Optional cosmetic DLC** if post-launch content is planned. Never gameplay-affecting.
- **No energy systems.** The player plays as much as they want.
- **No ads.** Ever. Ads destroy the experience.
- **No loot boxes or gacha.** Randomness is for gameplay, not monetization.
- **Expansion DLC is acceptable** if it provides substantial new content (new biomes, new classes, new story chapters). Price fairly.

This is harder to monetize than F2P. The trade-off is TRUST. Players who buy once and love the game become evangelists. Word-of-mouth is the most powerful marketing for indie games.

---

## 10. The Director's Playbook

### Pre-Production Checklist

Before writing a single line of production code:

- [ ] **One-sentence pitch written and team-aligned**
- [ ] **Design pillars defined (3-5)**
- [ ] **Core loop diagrammed and tested via prototype**
- [ ] **"One Big Assumption" identified and tested**
- [ ] **High Concept Document complete**
- [ ] **Target audience defined (be specific)**
- [ ] **Comparable titles analyzed (what to learn, what to avoid)**
- [ ] **Ship date set (and written on the wall)**
- [ ] **Feature budget allocated (see Section 6)**
- [ ] **Art direction references collected**
- [ ] **Vertical slice scope defined**
- [ ] **Risk register started**

### Weekly Director Review

Every week, the director should review:

```
1. PLAY THE BUILD (30 min minimum)
   - Is the core loop still fun?
   - What feels wrong?
   - What surprised me?

2. REVIEW PROGRESS (vs. milestone goals)
   - Are we on track?
   - What's blocked?
   - What needs my decision?

3. REVIEW SCOPE (vs. feature budget)
   - Has anything been added without cutting something?
   - Is any feature taking longer than budgeted?
   - Does anything need to be cut or re-scoped?

4. CHECK TEAM HEALTH
   - Is anyone overloaded?
   - Is anyone blocked?
   - Is morale okay?
   - Is anyone crunching? (If yes, scope is wrong.)

5. UPDATE GDD (if any decisions were made this week)
```

### Playtest Protocol

Playtesting is the director's most powerful tool. Do it RIGHT:

#### Preparation
- Identify what you're testing (combat feel? difficulty? FTUE? specific mechanic?)
- Prepare the build (stable, representative)
- Write 3-5 specific questions you need answered
- Recruit testers who match your target audience

#### During the Playtest
- **DO NOT explain the game.** If you have to explain it, it's not clear enough.
- **DO NOT help.** Let them struggle. That's the data.
- **DO NOT react.** No "oh, you're supposed to go left." No "that's a known bug."
- **WATCH.** Where do they hesitate? Where do they get confused? Where do they smile?
- **TAKE NOTES.** Timestamped. "2:34 — player tried to tap enemy to attack, nothing happened."

#### After the Playtest
- Ask your prepared questions
- Ask: "What was the most fun part?" and "What was the most frustrating part?"
- Ask: "Would you play this again? Why or why not?"
- Thank the tester. Their time is valuable.

#### Processing Results
- Look for PATTERNS, not individual opinions
- If 3+ testers have the same problem, it's a real problem
- Distinguish between "this is confusing" (design problem) and "this is hard" (might be intentional)
- Prioritize fixes that affect the core loop over fixes that affect edge cases

### The Director's Decision Log

Track every significant decision. Future you will thank present you.

```markdown
## Decision: [Title]
**Date:** [Date]
**Context:** [What prompted this decision?]
**Options Considered:**
1. [Option A] — Pros: ... / Cons: ...
2. [Option B] — Pros: ... / Cons: ...
**Decision:** [What we chose]
**Rationale:** [Why]
**Revisit if:** [Under what conditions should we reconsider?]
```

### Emergency Protocols

#### "The game isn't fun"
1. Identify WHICH PART isn't fun. The whole game? One system? One level?
2. Return to the core loop. Is the VERB fun in isolation?
3. If the verb isn't fun: This is a fundamental problem. Revisit the prototype. Consider pivoting.
4. If the verb IS fun but the game isn't: Something is getting in the way. Remove obstacles. Simplify. Let the fun breathe.

#### "We're going to miss the milestone"
1. Don't panic. Don't crunch.
2. List everything remaining for the milestone.
3. Separate MUST from SHOULD from COULD.
4. Cut COULDs immediately. Re-evaluate SHOULDs.
5. If MUSTs alone still don't fit: the milestone scope was wrong. Re-scope the milestone. Document why.

#### "The team disagrees on direction"
1. Hear all positions fully. Don't rush to decide.
2. Return to the design pillars. Which position best serves the pillars?
3. If pillars don't resolve it: prototype both approaches if time allows.
4. If no time: the director decides. Explain the reasoning. Move on.
5. **No revisiting.** Once decided, commit. Relitigating kills momentum.

#### "A feature isn't working"
1. Define "isn't working." Technically broken? Not fun? Doesn't fit?
2. If technically broken: Give engineering a fixed time box. If they can't fix it in [X], cut it.
3. If not fun: Is it the concept or the execution? Prototype a simpler version.
4. If doesn't fit: Cut it. Good ideas that don't serve the vision are still cuts.

---

## Appendix A: Recommended Reading & Study

### Books
- *The Art of Game Design* by Jesse Schell — Comprehensive game design theory
- *Blood, Sweat, and Pixels* by Jason Schreier — Real stories of game development
- *A Theory of Fun for Game Design* by Raph Koster — Why games are fun
- *Rules of Play* by Katie Salen & Eric Zimmerman — Formal game design theory
- *Agile Game Development with Scrum* by Clinton Keith — Agile adapted for games

### GDC Talks (Search YouTube / GDC Vault)
- "Failing to Fail: The Spiderweb Software Way" — Jeff Vogel (sustainable indie development)
- "Roguelikes and Narrative Design" — Greg Kasavin (Hades narrative integration)
- "30 Things I Hate About Your Game Pitch" — Brian Upton (pitching discipline)
- "The Art of Screenshake" — Jan Willem Nijman (game feel / juice)
- "Juice It or Lose It" — Martin Jonasson & Petri Purho (making games feel good)

### Reference Games (Study These)
- **Hades** — Narrative integration with roguelike loop
- **Slay the Spire** — Deckbuilding roguelike, perfect mobile adaptation
- **Dead Cells** — Action roguelite, excellent mobile port
- **Shattered Pixel Dungeon** — Mobile-native roguelike, open source
- **Vampire Survivors** — Minimalist design, maximum engagement
- **Into the Breach** — Turn-based roguelike, perfect information design

---

## Appendix B: Quick-Reference Decision Cards

### "Should we add this feature?"
```
1. Does it serve a design pillar?         → NO → Don't add it.
2. Does it improve the core loop?         → NO → Defer it.
3. What does it COST (in sprints)?        → [X sprints]
4. What do we CUT to pay for it?          → [Specific thing]
5. Is the trade-off worth it?             → NO → Don't add it.
                                          → YES → Add it. Update GDD.
```

### "Should we cut this feature?"
```
1. Is the core loop worse without it?     → YES → Keep fighting for it.
2. Have players noticed/missed it?        → NO → Cut it.
3. Is it blocking other work?             → YES → Cut it now.
4. Is it 80% done?                        → Ship the 80% version.
5. Is it 20% done?                        → Cut it. Salvage what you can.
```

### "Is this done?"
```
1. Does it work? (no crashes, no data loss)     → NO → Not done.
2. Does it feel right? (responsive, satisfying)  → NO → Not done.
3. Does it teach itself? (no manual required)    → NO → Iterate.
4. Would I be proud to show this?                → NO → Polish more.
5. Would shipping without it hurt the game?      → NO → Cut it. Ship the rest.
```

---

*"Logic is the beginning of wisdom, not the end."*
*Applied to game direction: frameworks are the beginning of good decisions, not a substitute for judgment. Use these tools. Trust the process. But always play the game. The game will tell you what it needs.*
