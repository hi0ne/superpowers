---
name: writing-skills
description: "Use when creating new skills, editing existing skills, or verifying skills work before deployment"
---

# Writing Skills

## Overview

**Writing skills IS Test-Driven Development applied to process documentation.**

You write test cases (pressure scenarios with subagents), watch them fail (baseline behavior), write the skill (documentation), watch tests pass (agents comply), and refactor (close loopholes).

**Core principle:** If you didn't watch an agent fail without the skill, you don't know if the skill teaches the right thing.

## What is a Skill?

A **skill** is a reference guide for proven techniques, patterns, or tools. Skills help future agent instances find and apply effective approaches.

**Skills are:** Reusable techniques, patterns, tools, reference guides

**Skills are NOT:** Narratives about how you solved a problem once

## When to Create a Skill

**Create when:**
- Technique wasn't intuitively obvious
- You'd reference this again across projects
- Pattern applies broadly (not project-specific)
- Others would benefit

**Don't create for:**
- One-off solutions
- Standard practices well-documented elsewhere
- Project-specific conventions (put in copilot-instructions.md)
- Mechanical constraints (if enforceable with tooling, automate it)

## Directory Structure

Place skills in `.github/skills/<skill-name>/`:

```
.github/skills/
  skill-name/
    SKILL.md              # Main reference (required)
    supporting-file.*     # Only if needed
```

## SKILL.md Format

**Frontmatter (YAML):**
```yaml
---
name: skill-name-with-hyphens    # Required: must match folder name
description: "Use when [specific triggering conditions]"  # Required: max 1024 chars
---
```

**Body structure:**
```markdown
# Skill Name

## Overview
What is this? Core principle in 1-2 sentences.

## When to Use
Bullet list with SYMPTOMS and use cases.
When NOT to use.

## Core Pattern
Before/after code comparison.

## Quick Reference
Table or bullets for scanning.

## Common Mistakes
What goes wrong + fixes.
```

## Description Best Practices

**CRITICAL: Description = When to Use, NOT What the Skill Does**

The description should ONLY describe triggering conditions. Do NOT summarize the skill's workflow.

```yaml
# ❌ BAD: Summarizes workflow
description: "Use when executing plans - dispatches subagent per task with code review"

# ✅ GOOD: Just triggering conditions
description: "Use when executing implementation plans with independent tasks in the current session"

# ❌ BAD: Too vague
description: "For async testing"

# ✅ GOOD: Specific triggers
description: "Use when tests have race conditions, timing dependencies, or pass/fail inconsistently"
```

## Keyword Coverage

Use words the agent would search for:
- Error messages: "Hook timed out", "ENOTEMPTY", "race condition"
- Symptoms: "flaky", "hanging", "zombie", "pollution"
- Synonyms: "timeout/hang/freeze", "cleanup/teardown/afterEach"

## Naming

**Use active voice, verb-first:**
- ✅ `condition-based-waiting` not `async-test-helpers`
- ✅ `root-cause-tracing` not `debugging-techniques`

## Cross-Referencing Other Skills

Reference by name only:
- ✅ Good: `**REQUIRED SUB-SKILL:** Use test-driven-development`
- ❌ Bad: Vague references like "See testing skill"

## Token Efficiency

Keep SKILL.md concise:
- Frequently-loaded skills: <200 words
- Other skills: <500 words
- Move heavy reference (100+ lines) to separate files
- Use cross-references instead of repeating content
