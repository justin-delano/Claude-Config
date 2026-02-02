---
name: meta-agent-generator
description: Meta-agent system for generating, refining, and self-improving specialist agents through human feedback loops. Load when creating new agents or improving existing ones.
---

# Agent Generator Meta-System

The Agent Generator is a meta-agent that creates, manages, and improves specialist agents through iterative feedback loops. Inspired by the Virtual Lab (Nature 2025), this system uses pure prompt engineering without model fine-tuning.

## Core Workflow

The agent lifecycle follows four phases:

### 1. Generation

When you need a specialist agent, invoke the Agent Generator through an interview process. The meta-agent will ask targeted questions to understand:

- Domain expertise requirements
- Target user interactions and use cases
- Expected output formats and constraints
- Evaluation criteria for success

Based on this analysis, the Agent Generator creates an agent configuration with:

- System prompt defining role and behavior
- Few-shot examples for in-context learning
- Temperature and sampling parameters
- Capability constraints and safety guidelines

### 2. Feedback Collection

After each interaction with a generated agent, collect structured feedback:

- Numerical rating (1-5) on interaction quality
- Qualitative comments on what worked or failed
- Context tags for categorization (explanation, analysis, creative, etc.)

Feedback is stored in append-only JSONL format at `~/.claude/agents/<agent-name>/feedback.jsonl`.

### 3. Refinement

When sufficient feedback accumulates (typically 10+ interactions), trigger refinement analysis. The Agent Generator:

- Analyzes feedback patterns for common issues
- Identifies underperforming capabilities
- Generates targeted prompt improvements
- Proposes version increments with human approval

All refinements require explicit human approval before application.

### 4. Self-Improvement

The Agent Generator analyzes its own generation performance across all agents:

- Extracts successful prompt patterns from high-rated agents
- Identifies anti-patterns from low-rated configurations
- Updates generation templates and strategies
- Requires human approval for self-modifications

## Directory Structure

```
~/.claude/
├── agents/                          # Generated specialist agents
│   └── <agent-name>/
│       ├── config.yaml              # Agent configuration
│       ├── feedback.jsonl           # Human feedback history
│       └── versions.jsonl           # Version history
└── meta/
    └── agent-generator/             # Meta-agent itself
        ├── config.yaml              # Meta-agent configuration
        ├── SKILL.md                 # This documentation
        ├── README.md                # User guide
        ├── prompts/                 # Prompt templates
        │   ├── interview-requirements.txt
        │   ├── generate-agent.txt
        │   ├── collect-feedback.txt
        │   ├── analyze-feedback.txt
        │   ├── generate-refinements.txt
        │   ├── self-analysis.txt
        │   └── refine-self.txt
        ├── templates/               # Generation patterns
        │   ├── agent-config-template.yaml
        │   ├── pattern-technical-domain.yaml
        │   ├── pattern-creative-writing.yaml
        │   └── pattern-data-analysis.yaml
        └── self-feedback.jsonl      # Meta-agent performance
```

## Agent Configuration Schema

Each agent has a YAML configuration:

```yaml
name: immunologist
version: 3
system_prompt: |
  You are an expert immunologist...
role_definition: |
  As an immunologist, you provide...
temperature: 0.7
few_shot_examples:
  - input: "What is T cell activation?"
    output: "T cell activation requires..."
capabilities:
  - literature-review
  - experimental-design
constraints:
  - cite-sources
  - acknowledge-limitations
feedback_summary:
  total_interactions: 24
  average_rating: 4.6
  common_strengths:
    - clear explanations
    - source citations
  common_weaknesses:
    - oversimplification
```

## Feedback Schema

Each feedback entry is a JSONL line:

```json
{
  "timestamp": "2026-02-02T16:15:00Z",
  "rating": 5,
  "comment": "Clear explanation with good citations",
  "context_tags": ["explanation", "mechanism", "literature"]
}
```

## Safety Mechanisms

- All refinements require human approval
- All meta-agent self-updates require human approval
- Git version control enables rollback
- JSONL append-only prevents data loss
- Explicit constraints in all agent configs

## Usage Patterns

### Creating a New Agent

When you need domain-specific assistance:

1. Identify the domain and required expertise
2. Request agent generation through interview process
3. Review and approve the generated configuration
4. Use the agent for tasks
5. Provide feedback after each interaction

### Improving an Existing Agent

When an agent underperforms:

1. Collect more feedback data points
2. Request refinement analysis
3. Review proposed changes
4. Approve or reject refinements
5. Monitor performance changes

### Meta-Agent Self-Improvement

The meta-agent periodically self-reflects:

1. Analyze performance across all agents
2. Extract successful patterns
3. Update generation strategies
4. Review and approve self-modifications
