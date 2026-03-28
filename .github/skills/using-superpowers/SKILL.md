---
name: using-superpowers
description: "Use when starting any conversation - establishes how to find and use skills, requiring skill invocation before ANY response including clarifying questions"
---

# Using Skills

## The Rule

**Check for relevant skills BEFORE any response or action.** Even a 1% chance a skill might apply means you should check. If a checked skill turns out to be wrong for the situation, you don't need to use it.

## Instruction Priority

Skills override default behavior, but **user instructions always take precedence**:

1. **User's explicit instructions** (copilot-instructions.md, AGENTS.md, direct requests) — highest priority
2. **Skills** — override default system behavior where they conflict
3. **Default system prompt** — lowest priority

## How Skills Work in VS Code Copilot

Skills are stored in `.github/skills/<name>/SKILL.md`. The agent discovers them automatically via their `description` field and loads them when relevant. You can also invoke skills as slash commands by typing `/` in chat.

## Red Flags

These thoughts mean STOP—you're rationalizing:

| Thought | Reality |
|---------|---------|
| "This is just a simple question" | Questions are tasks. Check for skills. |
| "I need more context first" | Skill check comes BEFORE clarifying questions. |
| "Let me explore the codebase first" | Skills tell you HOW to explore. Check first. |
| "This doesn't need a formal skill" | If a skill exists, use it. |
| "I remember this skill" | Skills evolve. Read current version. |
| "The skill is overkill" | Simple things become complex. Use it. |
| "I'll just do this one thing first" | Check BEFORE doing anything. |

## Skill Priority

When multiple skills could apply:

1. **Process skills first** (brainstorming, systematic-debugging) - determine HOW to approach
2. **Implementation skills second** - guide execution

"Let's build X" → brainstorming first, then implementation skills.
"Fix this bug" → systematic-debugging first, then domain-specific skills.

## Skill Types

**Rigid** (test-driven-development, systematic-debugging): Follow exactly. Don't adapt away discipline.

**Flexible** (patterns): Adapt principles to context.

The skill itself tells you which.

## User Instructions

Instructions say WHAT, not HOW. "Add X" or "Fix Y" doesn't mean skip workflows.
