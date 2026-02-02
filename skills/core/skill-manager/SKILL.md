---
name: skill-manager
description: Meta-skill for managing the skills ecosystem. Proactively creates, organizes, and tunes conditional skills based on patterns, requests, and domain complexity.
---

# Skills Management

## When to Create New Skills

Create a new skill when any of the following conditions are met:

1. **Repeated patterns** — You notice recurring patterns, conventions, or preferences in the user's requests that aren't yet codified in existing skills.
2. **Explicit request** — The user directly asks you to create a skill for a specific topic.
3. **Domain complexity** — A topic becomes complex enough that dedicated documentation would improve clarity, consistency, or recall.

## Skill Structure

Every skill must include frontmatter with metadata:

```yaml
---
name: <identifier>
description: <when to load this skill, what it covers>
---
```

The `name` should be a short, unique identifier (kebab-case). The `description` should indicate when and why the skill is relevant.

## Organization

Skills are organized into two directories under `skills/`:

**`skills/core/`** — Always-read skills (loaded at session start):
```
skills/core/
  git/SKILL.md           # Git version control
  style/SKILL.md         # Style and conventions
  skill-manager/SKILL.md # This file: skills management
```

**`skills/conditional/`** — Domain-specific skills (loaded when relevant):
```
skills/conditional/
  python/SKILL.md        # Python-specific conventions
  nix/SKILL.md           # Nix-specific conventions
  rust/SKILL.md          # Rust-specific conventions
  # ... add domains as needed
```

When creating a new skill:
- Core skills: place in `skills/core/` and add to "Core (Always Read)" in `CLAUDE.md`
- Conditional skills: place in `skills/conditional/` and add to "Other Topics (Conditional)" in `CLAUDE.md`

If no existing domain fits, create a new one using descriptive, lowercase names.

## Referencing Skills

### Core Skills (Always Read)

List in `CLAUDE.md` under "Core (Always Read)" with explicit paths:

```markdown
Always read these documents at session start, before working on any task:
- Git: @~/.claude/skills/core/git/SKILL.md
- Style: @~/.claude/skills/core/style/SKILL.md
```

### Conditional Skills

List in `CLAUDE.md` under "Other Topics (Conditional)" with domain context:

```markdown
For domain-specific tasks, proactively read:
- Python: @~/.claude/skills/conditional/python/SKILL.md
- Nix: @~/.claude/skills/conditional/nix/SKILL.md
```

## Proactive Tuning

Periodically review and refine skills for clarity and completeness:

1. **During application** — When applying a skill and encountering ambiguity, gaps, or friction, note the issue for refinement.
2. **After user feedback** — When the user corrects or refines guidance, update the relevant skill to capture the learning.
3. **Session reflection** — After complex or novel tasks, consider whether new patterns emerged that warrant skill creation or updates.

## Skill Lifecycle

- **Draft**: New skills can be created incrementally. Start with the most critical content.
- **Refine**: Iteratively improve based on usage and feedback.
- **Promote**: When a conditional skill becomes universally applicable, consider promoting it to the Core section of `CLAUDE.md`.
- **Deprecate**: If a skill becomes obsolete or redundant with others, remove it and update `CLAUDE.md` accordingly.
