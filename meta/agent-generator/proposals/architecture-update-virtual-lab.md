# Agent Generator Architecture Update: Virtual Lab Integration

**Proposal for enhancing the meta-agent system with Virtual Lab-inspired features**

## Current State Analysis

### Strengths
- Well-structured prompt templates for generation and critique
- Triangulated critique concept (domain agents + prompt-critic + user feedback)
- Token efficiency targets (~4000-5000 tokens)
- Multi-round training protocol
- Synthetic feedback generation

### Gaps Identified
- No multi-source interview protocol (single interviewer only)
- No agent deliberation (critiques are collected independently)
- Manual critic selection (no automation)
- Unclear trigger conditions for different refinement paths
- No explicit distillation/compression rounds
- Limited orchestration between workflow stages

## Proposed Architecture Changes

### 1. Multi-Source Interview Protocol

**New prompt:** `prompts/multi-source-interview.txt`

Concept: Instead of a single interviewer, multiple domain agents interview the user from different perspectives to triangulate requirements.

```yaml
multi_source_interview:
  interviewers:
    - domain_interviewer: Asks about domain expertise, terminology, workflows
    - design_interviewer: Asks about interaction patterns, output formats, constraints
    - evaluation_interviewer: Asks about success criteria, evaluation metrics
  process:
    1. Sequential or parallel questioning based on complexity
    2. Cross-interviewer synthesis of requirements
    3. Identified gaps trigger follow-up questions
  output:
    - consolidated_requirements.yaml
    - domain_specification.md
    - evaluation_criteria.md
```

### 2. Agent Deliberation Protocol

**New prompt:** `prompts/agent-deliberation.txt`

Concept: Before consensus, critics discuss and debate with each other to resolve conflicts and deepen analysis.

```yaml
deliberation_phase:
  participants:
    - domain_critics: [2-3 selected agents]
    - prompt_critic: design specialist
    - facilitator: agent_generator
  process:
    1. Initial independent critiques (current flow)
    2. Deliberation round: critics exchange critiques and discuss
    3. Focus areas:
       - Conflict resolution (domain vs design trade-offs)
       - Priority ranking (what matters most)
       - Cross-domain insights (synergies identified)
    4. Facilitator synthesizes deliberation into consensus
  output:
    - deliberation_transcript.md
    - consensus_report.md
    - refinement_proposal.yaml
```

### 3. Critic Selection Automation

**New file:** `prompts/critic-selection.txt`

Define rules for automatic critic selection based on domain similarity and diversity.

```yaml
critic_selection_rules:
  target: <agent being critiqued>

  selection_criteria:
    domain_similarity:
      - Moderate overlap (20-40%): Relevance
      - Low overlap (0-20%): Objectivity
      - Avoid identical domains: Echo chamber prevention

    diversity_requirements:
      - At least 2 different domain perspectives
      - Include prompt-critic for design quality
      - Include complementary methodological approaches

    performance_weighting:
      - High-rated agents (4.5+): Weighted 2x
      - Mid-rated agents (3.5-4.5): Weighted 1x
      - Low-rated agents (<3.5): Excluded from critique

  selection_algorithm:
    1. Calculate domain similarity score with all agents
    2. Identify moderate-overlap candidates (0.2-0.4)
    3. Select 2-3 diverse critics from moderate-overlap pool
    4. Always include prompt-critic
    5. Apply performance weighting to critique influence
```

### 4. Trigger Conditions Framework

**New file:** `prompts/trigger-conditions.txt`

Define when to trigger different refinement paths.

```yaml
trigger_conditions:
  initial_generation:
    trigger: "new agent requested"
    action: "run multi-source interview protocol"

  critique_round:
    trigger:
      - "version >= 2 AND feedback_count >= 5"
      - "manual request"
      - "after training round >= 3"
    action: "run deliberation protocol"

  distillation_round:
    trigger:
      - "token_count > 5000"
      - "every 3rd refinement round"
      - "manual request for compression"
    action: "run distillation protocol"

  self_improvement:
    trigger:
      - "3+ agents at version >= 5"
      - "pattern identified across agents"
    action: "analyze and update generation strategies"
```

### 5. Distillation Protocol

**New prompt:** `prompts/distillation.txt`

Explicit compression rounds focused purely on token efficiency.

```yaml
distillation_protocol:
  objective: "Reduce token count while preserving effectiveness"

  rounds: 3

  techniques:
    - Principle extraction: Convert examples to principles
    - Redundancy elimination: Remove overlapping content
    - Structural compression: Use concise formats
    - Capability pruning: Remove low-value capabilities
    - Constraint consolidation: Merge similar constraints

  evaluation:
    - Pre-compression: Capabilities assessment
    - Post-compression: Capabilities assessment
    - Compare: Ensure no capability loss

  output:
    - Token count reduction
    - Capability preservation score
    - Quality delta (pre vs post)
```

### 6. Multi-Round Orchestration

**New file:** `prompts/orchestration.txt`

Structured workflow coordinating the entire pipeline.

```yaml
orchestration_workflow:
  stage_1_requirements:
    - multi_source_interview
    - requirements_consolidation
    - domain_specification

  stage_2_generation:
    - initial_config_generation
    - baseline_validation

  stage_3_refinement_loop:
    repeat until satisfaction or max_rounds:
      - select_critics (auto)
      - generate_critiques
      - agent_deliberation
      - consensus_aggregation
      - refinement_proposal
      - human_approval
      - apply_refinements
      - update_feedback

      every 3rd round:
        - distillation_protocol

  stage_4_evaluation:
    - synthetic_feedback_generation
    - capability_assessment
    - token_efficiency_check

  state_tracking:
    - current_round
    - critics_selected
    - critiques_received
    - deliberation_status
    - consensus_level
    - token_count
    - capability_coverage
```

### 7. Enhanced Configuration Schema

**Update to:** `templates/agent-config-template.yaml`

Add new fields for tracking multi-round state.

```yaml
name: <agent-name>
version: <integer>
description: <one-sentence>

# Existing fields
system_prompt: |
  ...
role_definition: |
  ...
temperature: <float>
few_shot_examples: []
capabilities: []
constraints: []
feedback_summary: {}

# New fields for multi-round orchestration
multi_round_state:
  current_round: <integer>
  last_distillation_round: <integer>
  critics_history:
    - round: 1
      critics: [agent1, agent2, prompt-critic]
      consensus_level: strong/moderate/weak
    - round: 2
      critics: [agent3, agent4, prompt-critic]
      consensus_level: strong/moderate/weak

  deliberation_transcripts: []
  distillation_history: []

token_tracking:
  current_count: <integer>
  target_budget: 4000
  distillation_rounds: <integer>
  compression_ratio: <float>

requirement_sources:
  multi_source_interview:
    domain_interviewer: <requirements>
    design_interviewer: <requirements>
    evaluation_interviewer: <requirements>
    consolidated: <timestamp>
```

## Updated Workflow Diagram

```
┌─────────────────────────────────────────────────────────────────────┐
│                         STAGE 1: REQUIREMENTS                       │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────────────────────┐  │
│  │   Domain    │  │   Design    │  │        Evaluation            │  │
│  │ Interviewer │  │ Interviewer │  │        Interviewer           │  │
│  └──────┬──────┘  └──────┬──────┘  └──────────────┬──────────────┘  │
│         │                │                        │                  │
│         └────────────────┴────────────────────────┘                  │
│                            ▼                                         │
│                  ┌──────────────────┐                                │
│                  │ Consolidate      │                                │
│                  │ Requirements     │                                │
│                  └────────┬─────────┘                                │
└────────────────────────────┼──────────────────────────────────────────┘
                             ▼
┌─────────────────────────────────────────────────────────────────────┐
│                         STAGE 2: GENERATION                         │
│                  ┌──────────────────┐                                │
│                  │ Generate Initial │                                │
│                  │ Agent Config     │                                │
│                  └────────┬─────────┘                                │
└────────────────────────────┼──────────────────────────────────────────┘
                             ▼
┌─────────────────────────────────────────────────────────────────────┐
│                    STAGE 3: REFINEMENT LOOP                         │
│  ┌──────────────────────────────────────────────────────────────┐   │
│  │                                                              │   │
│  │  1. Auto-Select Critics (based on domain similarity)        │   │
│  │     ┌──────────┐  ┌──────────┐  ┌──────────────────┐        │   │
│  │     │ Domain   │  │ Domain   │  │  Prompt-Critic   │        │   │
│  │     │ Critic 1 │  │ Critic 2 │  │  (always)        │        │   │
│  │     └────┬─────┘  └────┬─────┘  └────────┬─────────┘        │   │
│  │          │             │                  │                   │   │
│  │          └─────────────┴──────────────────┘                   │   │
│  │                          ▼                                     │   │
│  │  2. Independent Critiques                                     │   │
│  │                          ▼                                     │   │
│  │  3. Agent Deliberation (NEW - critics discuss)               │   │
│  │     ┌─────────────────────────────────────────────────┐      │   │
│  │     │ Facilitator: Agent Generator                     │      │   │
│  │     │ - Exchange critiques                            │      │   │
│  │     │ - Discuss conflicts                             │      │   │
│  │     │ - Identify priorities                          │      │   │
│  │     │ - Build consensus                              │      │   │
│  │     └─────────────────────────────────────────────────┘      │   │
│  │                          ▼                                     │   │
│  │  4. Consensus Aggregation                                     │   │
│  │                          ▼                                     │   │
│  │  5. Refinement Proposal + Human Approval                     │   │
│  │                          ▼                                     │   │
│  │  6. Apply Refinements                                        │   │
│  │                          │                                     │   │
│  │            ┌─────────────┴─────────────┐                     │   │
│  │            │                           │                     │   │
│  │            ▼                           ▼                     │   │
│  │    Every 3rd round              Continue to                  │   │
│  │    ┌──────────────┐              next round                 │   │
│  │    │ Distillation │  ◄─────────────────────────────────────┘   │   │
│  │    │ Protocol     │                                         │   │
│  │    │ (NEW)        │                                         │   │
│  │    └──────┬───────┘                                         │   │
│  │           │                                                 │   │
│  └───────────┴─────────────────────────────────────────────────┘   │
│                              │                                       │
└──────────────────────────────┼───────────────────────────────────────┘
                               ▼
┌─────────────────────────────────────────────────────────────────────┐
│                      STAGE 4: EVALUATION                           │
│  ┌──────────────────┐  ┌──────────────────┐  ┌──────────────────┐  │
│  │ Synthetic        │  │ Capability       │  │ Token Efficiency │  │
│  │ Feedback         │  │ Assessment       │  │ Check            │  │
│  │ Generation       │  │                  │  │                  │  │
│  └──────────────────┘  └──────────────────┘  └──────────────────┘  │
└─────────────────────────────────────────────────────────────────────┘
```

## Implementation Plan

### Phase 1: Core Protocol Updates (Prompt Files)

1. **multi-source-interview.txt** - Three-perspective interview protocol
2. **agent-deliberation.txt** - Critics discuss before consensus
3. **distillation.txt** - Explicit compression protocol
4. **critic-selection.txt** - Automatic critic selection rules
5. **trigger-conditions.txt** - When to trigger what
6. **orchestration.txt** - End-to-end workflow coordination

### Phase 2: Documentation Updates

1. **SKILL.md** - Update with new workflows and protocols
2. **README.md** - Update usage guide with new features
3. **config.yaml** - Update meta-agent config with new capabilities

### Phase 3: (Future) Automation

1. Python/orchestration script for automated workflow
2. Domain similarity calculation for critic selection
3. State tracking across rounds
4. Automatic triggering based on conditions

## Questions for Approval

1. **Critic selection algorithm** - Should domain similarity be calculated based on:
   - Capability overlap (Jaccard similarity)?
   - Capability keyword matching?
   - Manual categorization of agents into domains?

2. **Deliberation format** - Should critics:
   - Exchange full critiques and respond point-by-point?
   - Focus on conflicts only?
   - Have structured debate rounds?

3. **Distillation frequency** - Is "every 3rd round" appropriate, or should it be:
   - Token-count triggered (>5000)?
   - Manual only?
   - Different schedule?

4. **Multi-source interview** - Should interviewers:
   - Question sequentially (one finishes before next starts)?
   - Question in parallel (all perspectives covered together)?
   - Hybrid (initial parallel, follow-ups sequential)?
