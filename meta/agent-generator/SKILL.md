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

**Token Budget Target:** ~4000-5000 tokens for system_prompt + few_shot_examples.

**Refinement Philosophy:**
- **Consolidate, don't accumulate**: Each round refines and replaces content
- Remove redundancy when adding new capabilities
- Maximum 2-3 few-shot examples (quality over quantity)
- Use concise principles over verbose explanations

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

### Multi-Round Agent Training

For comprehensive agent development, use multi-round training:

**Protocol:** See `prompts/multi-round-training.txt`

**Key principles:**
- Each round iteratively refines, not accumulates
- Updates feedback/versions/registry after EACH round (not just final)
- Stays within token budget (~4000-5000 tokens)
- Focus areas: Role → Examples → Capabilities → Consolidation → Feedback
- **Generate synthetic feedback during training** (30+ entries covering all domains)
- **Agent-to-agent critique** for improvement (see below)

**Example:**
```
Round 1: Core role definition and boundaries
Round 2: Few-shot example quality (reduce count, improve quality)
Round 3: Capability specificity and constraint sharpening
Round 4: Consolidation (remove redundancy)
Round 5-9: Domain-specific expansion with synthetic feedback
Round 10: Final consolidation + synthetic feedback generation
```

Each round creates a git commit, so full history is preserved.

#### Synthetic Feedback Generation

**Always generate synthetic feedback during multi-round training**, even before real user interactions:

1. **Purpose:** Provide feedback-driven refinement data for agent improvement
2. **Format:** JSONL entries matching real feedback schema
3. **Quantity:** 30+ entries covering all expertise domains
4. **Quality:** Mix of ratings (3-5) with constructive criticism
5. **Context tags:** Categorize each entry by domain/interaction type

```json
{
  "timestamp": "2026-02-02T16:15:00Z",
  "rating": 5,
  "comment": "Excellent workflow guidance with clear decision tree",
  "context_tags": ["workflow-design", "tool-selection", "practical-guidance"]
}
```

**When to generate:** At final training round OR continuously during multi-round training

#### Agent-to-Agent Critique

**Agents can critique other agents for improvement:**

1. **Cross-agent review:** One specialist agent reviews another's configuration
2. **Structural critique:** Analyze prompt structure, capabilities, examples
3. **Domain overlap:** Identify redundancy or gaps across agents
4. **Best practice transfer:** Apply successful patterns from high-performing agents

**Usage:**
```bash
# Use when creating new agents or improving existing ones
# Ask: "Have the geneticist agent critique the immunologist agent configuration"
```

**Critique output format:** Structured feedback with:
- Strengths to preserve
- Weaknesses to address
- Redundancy with other agents
- Missing capabilities
- Suggested improvements

### Meta-Agent Self-Improvement

The meta-agent periodically self-reflects:

1. Analyze performance across all agents
2. Extract successful patterns
3. Update generation strategies
4. Review and approve self-modifications
