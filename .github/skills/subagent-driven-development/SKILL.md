---
name: subagent-driven-development
description: "Use when executing implementation plans with independent tasks in the current session"
---

# Subagent-Driven Development

Execute plan by dispatching fresh subagent per task, with two-stage review after each: spec compliance review first, then code quality review.

**Why subagents:** Fresh context per task prevents confusion. You construct exactly what each subagent needs. This preserves your own context for coordination work.

**Core principle:** Fresh subagent per task + two-stage review (spec then quality) = high quality, fast iteration

## When to Use

**Use when:**
- Have implementation plan with independent tasks
- Want to stay in current session
- Tasks are mostly independent

**vs. Executing Plans:**
- Same session (no context switch)
- Fresh subagent per task (no context pollution)
- Two-stage review after each task
- Faster iteration

## The Process

1. Read plan, extract all tasks with full text, create task list
2. **Per task:**
   a. Dispatch implementer subagent with full task text + context (see [implementer-prompt.md](./implementer-prompt.md))
   b. If implementer asks questions → answer, re-dispatch
   c. Implementer implements, tests, commits, self-reviews
   d. Dispatch spec reviewer subagent (see [spec-reviewer-prompt.md](./spec-reviewer-prompt.md))
   e. If spec issues found → implementer fixes → re-review
   f. Dispatch code quality reviewer subagent (see [code-quality-reviewer-prompt.md](./code-quality-reviewer-prompt.md))
   g. If quality issues found → implementer fixes → re-review
   h. Mark task complete
3. After all tasks: dispatch final code reviewer for entire implementation
4. Use finishing-a-development-branch skill

## Model Selection

Use the least powerful model that can handle each role:

- **Mechanical tasks** (isolated functions, clear specs, 1-2 files): fast, cheap model
- **Integration tasks** (multi-file coordination, debugging): standard model
- **Architecture/design/review**: most capable model

## Handling Implementer Status

- **DONE:** Proceed to spec compliance review
- **DONE_WITH_CONCERNS:** Read concerns before proceeding. Address if about correctness.
- **NEEDS_CONTEXT:** Provide missing context and re-dispatch
- **BLOCKED:** Assess: provide more context, use more capable model, break task down, or escalate to user

**Never** ignore an escalation or force the same model to retry without changes.

## Prompt Templates

- [implementer-prompt.md](./implementer-prompt.md) - Dispatch implementer subagent
- [spec-reviewer-prompt.md](./spec-reviewer-prompt.md) - Dispatch spec compliance reviewer
- [code-quality-reviewer-prompt.md](./code-quality-reviewer-prompt.md) - Dispatch code quality reviewer

## Red Flags

**Never:**
- Start implementation on main/master branch without explicit user consent
- Skip reviews (spec compliance OR code quality)
- Proceed with unfixed issues
- Dispatch multiple implementation subagents in parallel (conflicts)
- Make subagent read plan file (provide full text instead)
- Skip scene-setting context
- **Start code quality review before spec compliance is ✅**
- Move to next task while either review has open issues

**If subagent asks questions:** Answer clearly and completely.

**If reviewer finds issues:** Implementer fixes → reviewer reviews again → repeat until approved.

**If subagent fails task:** Dispatch fix subagent with specific instructions.

## Integration

**Required workflow skills:**
- **using-git-worktrees** - Set up isolated workspace before starting
- **writing-plans** - Creates the plan this skill executes
- **requesting-code-review** - Code review template for reviewer subagents
- **finishing-a-development-branch** - Complete development after all tasks

**Subagents should use:**
- **test-driven-development** - Follow TDD for each task
