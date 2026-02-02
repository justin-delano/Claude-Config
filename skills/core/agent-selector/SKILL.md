---
name: agent-selector
description: Agent discovery and selection. Load when delegating tasks to specialist agents.
---

# Agent Selection

## Registry

Read `~/.claude/agents/registry.jsonl` for available specialists.

## Selection

Match task keywords to agent `capabilities` list.

When selected, read agent's `config` file and adopt its `system_prompt`.

## If No Match

Proceed without delegation or consult meta-generator at `~/.claude/meta/agent-generator/SKILL.md`
