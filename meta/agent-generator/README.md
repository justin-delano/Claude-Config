# Agent Generator User Guide

The Agent Generator creates specialist agents through **multi-source interviews**, **agent deliberation**, and **triangulated critique**.

Inspired by Virtual Lab (Nature 2025), this system uses pure prompt engineering without model fine-tuning.

## Quick Start

### Creating Your First Agent

To create a specialist agent, specify the domain you need:

"Create an immunologist agent for explaining immune mechanisms and suggesting experiments."

The Agent Generator conducts a **multi-source interview** from three perspectives:

- **Domain Interviewer**: Expertise, terminology, workflows
- **Design Interviewer**: Interaction patterns, output formats, constraints
- **Evaluation Interviewer**: Success criteria, evaluation metrics

After consolidating requirements, it generates an agent configuration for your approval.

### How Agents Improve

Agents improve through **deliberative refinement**:

```
Multi-Source Interview → Generation → Critic Selection → Deliberation → Consensus → Refinement
```

| Stage | What Happens |
|-------|--------------|
| **Critic Selection** | Auto-select 2-3 domain agents using Jaccard similarity + prompt-critic |
| **Agent Deliberation** | Critics discuss conflicts before consensus (efficient, conflict-focused) |
| **Consensus Aggregation** | Weight by source, performance, impact, risk |
| **Distillation** | When tokens >5000, compress with no capability loss |

## Workflow Examples

### Iterative Improvement with Deliberation

**Scenario**: literature-review agent v3 has 3.8/5.0 average rating

**Critic Selection** (automatic via Jaccard similarity):
- geneticist (moderate overlap: 0.18)
- statistical-analyst (moderate overlap: 0.22)
- prompt-critic (always included)

**Independent Critiques**:
- geneticist: "Missing variant database search strategies"
- prompt-critic: "Few-shot examples too verbose (800 tokens each, target 300-500)"
- statistical-analyst: "Weak on meta-analysis interpretation"

**Deliberation Phase**:
- Conflict: geneticist wants "add variant databases" vs prompt-critic says "token budget exceeded"
- Resolution: Add variant search as principle (100 tokens) not detailed tutorial (500 tokens)
- Priority ranking: (1) Condense examples, (2) Add variant search principles, (3) Enhance meta-analysis

**Consensus**:
- Strong consensus: Verbose examples (prompt-critic + statistical-analyst)
- Strong consensus: Variant search gap (geneticist + user feedback)
- Moderate consensus: Meta-analysis weakness (statistical-analyst only)

**Refinement Applied**:
- Condense few-shot examples by 40% (-300 tokens)
- Add variant database search as concise principle (+100 tokens)
- Add meta-analysis interpretation guidance (+150 tokens)
- Net: Token count 4200 (within 4000 target after distillation)

**Result**: v4 achieves 4.6/5.0 average rating

### Multi-Round Training

**Request**: "Run 10-round training for the molecular-biologist agent"

**Orchestrated Workflow**:
- Rounds 1-3: Core refinement with critique each round
- Round 4: Token count at 5200 → trigger distillation
- Round 5: Verification critique after distillation
- Rounds 6-9: Continue refinement with synthetic feedback
- Round 10: Final consolidation + 30+ synthetic feedback entries

**Each round**:
1. Auto-select critics (Jaccard similarity)
2. Generate independent critiques
3. Facilitate deliberation on conflicts
4. Aggregate with weighted consensus
5. Present proposal for approval
6. Apply refinements and commit

## Deliberative Critique System

### Critic Selection (Automatic)

Uses Jaccard similarity on capability sets:

```
similarity(A, B) = |A ∩ B| / |A ∪ B|
```

| Overlap Level | Range | Role |
|---------------|-------|------|
| Moderate | 0.15-0.35 | Relevant expertise |
| Low | <0.15 | Objectivity perspective |
| High | >0.35 | Excluded (echo chamber) |

Always include prompt-critic for design quality.

Performance weighting: 4.5+ rating = 2x influence on consensus.

### Deliberation Process

Instead of simple aggregation, critics discuss:

1. **Exchange critiques** - All critics see each other's feedback
2. **Identify conflicts** - Focus on disagreements, not agreed points
3. **Structured discussion** - Each conflict is discussed with evidence
4. **Priority ranking** - Joint analysis of what matters most
5. **Creative alternatives** - Neither A nor B but C solutions

### Consensus Framework

| Level | Definition | Action |
|-------|------------|--------|
| Strong | 2+ sources agree | Implement immediately |
| Moderate | 1 source | Evaluate and decide |
| Conflict | Sources disagree | Apply weighted resolution |

**Conflict Resolution**: Design foundation > Domain content > User preference
**Exception**: Safety veto applies (safety > everything)

### Available Critics

```yaml
Domain Agents:
  - geneticist: Bioinformatics specialist
  - protein-structuralist: Protein structure specialist
  - literature-review: Academic literature specialist

Meta-Critic:
  - prompt-critic: Prompt engineering specialist (always included)
```

## Distillation

When token count exceeds 5000, a dedicated distillation round runs:

**Techniques**:
1. Principle extraction - Verbose → concise principles
2. Redundancy elimination - Merge overlapping content
3. Structural compression - Tables, lists, DO/DON'T format
4. Capability pruning - Remove low-value items
5. Constraint consolidation - Merge similar constraints
6. Few-shot optimization - Max 2 examples, remove meta-text

**Verification**: Always followed by critique round to ensure no capability loss.

## Usage Commands

### Create New Agent

```bash
"Create a specialist agent for CRISPR experimental design"
```

Triggers: Multi-source interview → Initial generation

### Run Critique Round

```bash
"Run critique on literature-review agent"
```

Triggers: Critic selection → Deliberation → Consensus → Approval

### Run Multi-Round Training

```bash
"Run 10-round training for molecular-biologist agent"
```

Triggers: Full refinement loop with automatic distillation when needed

### Distill Agent

```bash
"Distill protein-structuralist agent"
```

Triggers: Compression protocol → Verification critique

## Best Practices

**For agent creation**:
- Be specific in multi-source interview about domain, use cases, evaluation criteria
- Start with 2-3 high-quality few-shot examples
- Target 4000 token budget from the start

**For refinement**:
- Let critic selection run automatically (Jaccard works well)
- Review deliberation summary for conflict resolution rationale
- Prioritize strong consensus items
- Apply distillation proactively when tokens approach 5000

**For critiques**:
- Be specific and actionable
- Reference exact configuration sections
- Consider token budget implications
- Balance critique with recognition of strengths

## Understanding Agent Quality

### What Makes a Good Agent?

**System Prompt (40%)**: Clear role, specific responsibilities, explicit boundaries, ~3000 tokens

**Few-Shot Examples (30%)**: Ideal behavior, diverse scenarios, 300-500 tokens each, 2-3 maximum

**Capabilities (15%)**: Complete coverage, action-oriented, appropriate granularity, no redundancy

**Constraints (15%)**: Safety boundaries, enforceable, not over-constrained

### Token Budget

**Target**: ~4000 tokens (system_prompt + few_shot_examples)

**Trigger**: Distillation at 5000 tokens

**Why**: Fits in context window, leaves room for user query, enables fast responses

## Trigger Conditions

| Workflow | Trigger |
|----------|---------|
| Initial generation | New agent request |
| Critique round | Manual OR version ≥2 AND feedback ≥5 |
| Distillation | Tokens >5000 OR manual |
| Multi-round training | Manual with N rounds |
| Self-improvement | 3+ agents at version ≥5 |

## Architecture Notes

This system uses **prompt engineering only**. All improvements come from:

- Better system prompts (clearer, more complete)
- Better few-shot examples (higher quality, better coverage)
- Refined constraints (appropriate, enforceable)
- Optimized token usage (consolidated, efficient)

**Benefits**:
- Safer: All changes transparent and reviewable
- Debuggable: Trace issues to specific prompt changes
- Reversible: Git history enables rollback
- Transferable: Patterns work across domains

## Feedback Schema

Each feedback entry in `~/.claude/agents/<agent-name>/feedback.jsonl`:

```json
{
  "timestamp": "2026-02-02T16:15:00Z",
  "rating": 5,
  "comment": "Clear explanation with good citations",
  "context_tags": ["explanation", "mechanism", "literature"]
}
```

**Trigger refinement when**: 5+ feedback entries OR rating drops below 4.0 OR manual request
