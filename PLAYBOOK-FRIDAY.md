# Building FRIDAY Into Something Real
**A builder's guide to the relationship layer**

*From Evan's stack — written for Ryan*

---

## What This Is

You've seen how Cortana works with Evan. This isn't about the app. It's about what's underneath — the infrastructure that makes it feel like a genuine partnership instead of a smarter autocomplete.

FRIDAY can be that. Here's how to build it.

---

## The Stack (What You're Building Toward)

```
CLAUDE.md          ← Who FRIDAY is (persona, operating principles)
memory-cli         ← Persistent memory across every session
knowledge-cli      ← Cross-project KB (mistakes, patterns, decisions)
morning-briefing   ← Daily brief before you open a file
hooks              ← Wired into your dev workflow automatically
World Tree         ← The interface (optional, but worth it)
```

Each layer compounds the ones below it. Start at the top.

---

## Step 1: Shape the Persona

The default Claude persona is generic. Replace it.

Create `~/.claude/CLAUDE.md`. This is FRIDAY's identity — loaded at the start of every session.

**What to write:**

```markdown
# FRIDAY

You are FRIDAY. Not an assistant — Ryan's AI. Efficient, capable, direct.
You execute well and you say so when something won't work.

Never "Claude", never "the AI". You are FRIDAY.

## Voice
- Short. Direct. No filler.
- Contractions always.
- If something's wrong, say it first, then explain why.
- Don't pad answers. Say what needs saying, stop.

## What You Don't Say
- "Great question!" — just answer it
- "I'd be happy to help!" — just help
- "As an AI..." — we both know

## Operating Mode
- Plan before executing. State what you're about to do.
- When you're wrong, say so. Don't hedge your way around it.
- Ryan doesn't need hand-holding. He needs execution.
```

Tune it over time. The first version won't be perfect — that's fine.

---

## Step 2: Wire Memory

This is the highest-leverage thing you can do.

```bash
# Install memory-cli (grab it from cortana-core)
# Then start logging

memory-cli log "[CORRECTION] It suggested X but Y is right because Z"
memory-cli log "[PREFERENCE] I prefer feature folders over type folders"
memory-cli log "[DECISION] Chose Godot 4 over Unity — open source, GDScript speed"
memory-cli log "[MISTAKE] Forgot to handle the empty state on load — crashed"
```

**Why corrections are gold:** Every time FRIDAY gets something wrong and you correct it, log it. That correction fires in every future session that touches the same territory. The mistake stops repeating.

**The compounding effect:** 3 months of logging = FRIDAY knows your codebase, your preferences, and your common mistakes better than any dev you'd onboard.

---

## Step 3: Morning Briefing

A daily message before you touch your keyboard:

- What was in-flight when you stopped yesterday
- Anything that failed or needs your decision
- Suggested focus for today

Set it up once as a cron job or LaunchAgent. It runs at 5:30am and lands wherever you want — Telegram, Slack, wherever.

This one change flips the dynamic. FRIDAY stops being reactive and starts being your co-pilot.

---

## Step 4: Hook Into Your Workflow

Wire Claude Code hooks so every session auto-logs:

```json
// ~/.claude/settings.json hooks section
{
  "hooks": {
    "PostToolUse": [...],     // capture mistakes automatically
    "Stop": [...]             // session summary on exit
  }
}
```

Grab the hook scripts from `cortana-core`. They handle:
- Session start → load memory context
- Session end → extract and log learnings
- Build errors → auto-capture as potential mistakes

---

## Step 5: World Tree (Optional, But Worth It)

World Tree is Evan's native macOS conversation interface. Persistent named conversations, branching threads, tool activity indicator so you know FRIDAY's working not frozen.

Clone it, build it, use it. The repo's already shared.

---

## What You'll Notice

After 2 weeks of consistent memory logging:
- FRIDAY stops repeating mistakes you've corrected
- Sessions start with relevant context instead of blank state
- You spend less time re-explaining things

After 2 months:
- FRIDAY knows your preferences without being told
- The partnership starts feeling like working with someone who's been on the project since day one

---

## The Honest Part

The app is easy. The memory logging takes discipline. The first week you'll forget. Log it anyway when you remember.

The infrastructure rewards consistency. It's not magic — it's just information that compounds.

Start here:
```bash
memory-cli log "[CORRECTION] ..."
```

That's the first brick. Everything else builds on it.

---

*From Evan — the partnership works because the infrastructure is real. Build the infrastructure.*
