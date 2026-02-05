---
name: meta-agent-generator
description: Meta-agent system for generating, refining, and self-improving specialist agents through multi-source interviews, agent deliberation, and triangulated critique. Inspired by Virtual Lab (Nature 2025). Load when creating new agents or improving existing ones.
---

# Agent Generator Meta-System

The Agent Generator creates specialist agents through a four-stage workflow: multi-source requirements gathering, initial generation, deliberative refinement, and evaluation.

Inspired by Virtual Lab (Nature 2025), this system uses pure prompt engineering without model fine-tuning.

## Core Workflow

### Stage 1: Multi-Source Requirements Gathering

Instead of a single interview, requirements are gathered from three perspectives:

**Domain Interviewer** - Expertise, terminology, workflows
**Design Interviewer** - Interaction patterns, output formats, constraints
**Evaluation Interviewer** - Success criteria, evaluation metrics

The interview format is hybrid: parallel initial questioning covers all perspectives, followed by sequential follow-ups based on gaps identified.

**Output**: `agent_requirements.yaml` with consolidated domain, design, and evaluation specifications.

### Stage 2: Initial Generation

Based on consolidated requirements, generate the initial agent configuration:

- System prompt defining role and behavior
- Few-shot examples for in-context learning (2-3 maximum)
- Temperature and sampling parameters
- Capability constraints and safety guidelines

**Token Budget Target**: ~4000 tokens for system_prompt + few_shot_examples.

**Output**: `agents/<agent-name>/config.yaml` at version 1.

### Stage 3: Deliberative Refinement Loop

Repeat until satisfaction or max rounds:

1. **Select Critics** - Automatic selection using Jaccard similarity on capabilities
   - Moderate overlap (0.15-0.35): Relevant expertise
   - Low overlap (<0.15): Objectivity
   - Always include prompt-critic for design quality
   - Apply performance weighting (4.5+ rating = 2x influence)

2. **Generate Critiques** - Each critic provides structured feedback

3. **Agent Deliberation** - Critics discuss conflicts before consensus
   - Conflict-focused discussion (efficient, not exhaustive)
   - Priority ranking through joint analysis
   - Creative alternatives when positions differ

4. **Consensus Aggregation** - Weighted synthesis of critiques
   - Strong consensus (2+ sources agree): ACT immediately
   - Moderate consensus (1 source): EVALUATE and decide
   - Conflict: Resolve through framework (Design foundation > Domain content > User preference)

5. **Human Approval** - Present refinement proposal

6. **Apply Refinements** - Update config, increment version, commit

7. **Distillation Check** - If tokens >5000, run compression protocol

**Distillation**: When token count exceeds 5000, run dedicated compression round using principle extraction, redundancy elimination, structural compression, capability pruning, and constraint consolidation. Always verify distillation with a follow-up critique.

### Stage 4: Evaluation

- **Synthetic feedback generation** - 30+ entries covering all capability domains
- **Capability assessment** - Compare pre/post to ensure no degradation
- **Token efficiency check** - Verify within budget

## Directory Structure

```
~/.claude/
├── agents/                          # Generated specialist agents
│   ├── prompt-critic/               # Prompt engineering specialist
│   │   ├── config.yaml
│   │   ├── feedback.jsonl
│   │   └── versions.jsonl
│   ├── geneticist/                  # Bioinformatics specialist
│   ├── protein-structuralist/       # Protein structure specialist
│   ├── literature-review/           # Academic literature specialist
│   └── <agent-name>/                # Other specialist agents
│       ├── config.yaml
│       ├── feedback.jsonl
│       └── versions.jsonl
└── meta/
    └── agent-generator/             # Meta-agent itself
        ├── config.yaml              # Meta-agent configuration
        ├── SKILL.md                 # This documentation
        ├── prompts/                 # Prompt templates
        │   ├── multi-source-interview.txt
        │   ├── generate-agent.txt
        │   ├── critic-selection.txt
        │   ├── agent-critique.txt
        │   ├── agent-deliberation.txt
        │   ├── triangulated-critique.txt
        │   ├── generate-refinements.txt
        │   ├── distillation.txt
        │   ├── trigger-conditions.txt
        │   ├── orchestration.txt
        │   └── multi-round-training.txt
        └── templates/
            └── agent-config-template.yaml
```

## Configuration Schema

```yaml
name: immunologist
version: 3
description: One-sentence description

system_prompt: |
  You are an expert immunologist...
  [Concise, principles over prose]

role_definition: |
  As an immunologist, you specialize in...

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
  common_weaknesses:
    - oversimplification

multi_round_state:
  current_round: 5
  last_distillation_round: 3
  critics_history:
    - round: 4
      critics: [geneticist, prompt-critic]
      consensus_level: strong

token_tracking:
  current_count: 3800
  target_budget: 4000
  distillation_rounds: 1
```

## Usage Commands

### Create New Agent

```bash
"Create a specialist agent for <domain>"
```

Triggers: Multi-source interview → Initial generation

### Run Multi-Round Training

```bash
"Run 10-round training for <agent>"
```

Triggers: Refinement loop for N rounds with automatic distillation when tokens exceed 5000

### Run Single Critique Round

```bash
"Run critique on <agent>"
```

Triggers: Single refinement round with automatic critic selection

### Distill Agent

```bash
"Distill <agent>"
```

Triggers: Compression protocol followed by verification critique

## Trigger Conditions

| Workflow | Trigger |
|----------|---------|
| Initial generation | New agent request |
| Critique round | Manual request OR version ≥2 AND feedback ≥5 |
| Distillation | Tokens >5000 OR manual request |
| Multi-round training | Manual request with specified rounds |
| Self-improvement | 3+ agents at version ≥5 OR pattern identified |

## Critic Selection Algorithm

Critic selection uses Jaccard similarity on capability sets:

```
similarity(A, B) = |A ∩ B| / |A ∪ B|
```

- **Moderate overlap (0.15-0.35)**: Relevant expertise
- **Low overlap (<0.15)**: Objectivity perspective
- **High overlap (>0.35)**: Excluded (too similar, echo chamber)

Always include prompt-critic for design quality assessment.

## Consensus Framework

| Level | Definition | Action |
|-------|------------|--------|
| Strong | 2+ sources agree | Implement immediately |
| Moderate | 1 source identifies issue | Evaluate and decide |
| Conflict | Sources disagree | Apply weighted resolution |

**Conflict Resolution**: Design quality > Domain content > User preference
**Exception**: Safety veto applies (safety > everything)

## Distillation Techniques

When tokens exceed 5000:

1. **Principle extraction** - Replace verbose explanations with concise principles
2. **Redundancy elimination** - Merge overlapping content
3. **Structural compression** - Use tables, lists, DO/DON'T format
4. **Capability pruning** - Remove low-value or redundant capabilities
5. **Constraint consolidation** - Merge similar constraints
6. **Few-shot optimization** - Maximum 2 examples, remove explanatory text

Target: Reduce to ≤4000 tokens with no capability loss.

## Safety Mechanisms

- All refinements require human approval
- All meta-agent self-updates require human approval
- Git version control enables rollback
- JSONL append-only prevents data loss
- Explicit constraints in all agent configs
- Safety veto overrides all consensus decisions

## Best Practices

**For agent generation**:
- Use multi-source interviews for comprehensive requirements
- Target 4000 token budget from the start
- Start with 2-3 high-quality few-shot examples
- Use principles over exhaustive lists

**For refinement**:
- Let critic selection run automatically
- Review deliberation summary before consensus
- Prioritize strong consensus items
- Apply distillation proactively when tokens grow

**For critiques**:
- Be specific and actionable
- Reference exact configuration sections
- Balance critique with recognition of strengths
- Consider token budget implications

## Available Critics

```yaml
available_critics:
  prompt-critic: Prompt engineering specialist (design quality, token efficiency)
  geneticist: Bioinformatics specialist (functional genomics, QTL analysis)
  protein-structuralist: Protein structure specialist (modeling, experimental methods)
  literature-review: Academic literature specialist (search, synthesis, critique)
```

## Quality Signals

**Effective interview indicators**:
- All three perspectives (domain, design, evaluation) represented
- Requirements are specific rather than generic
- Success criteria are measurable

**Effective critique indicators**:
- Multiple critics identify same issue independently
- Deliberation resolves conflicts constructively
- Strong consensus on critical improvements
- Token-conscious recommendations

**Effective distillation indicators**:
- Token count reduced to ≤4000
- No capability loss
- Prompt remains clear and coherent
- Examples still demonstrate key behaviors
