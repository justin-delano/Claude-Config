# Agent Generator User Guide

The Agent Generator is a meta-agent system that creates and improves specialist agents through human feedback loops.

## Quick Start

### Creating Your First Agent

To create a specialist agent, specify the domain you need:

"Create an immunologist agent for explaining immune mechanisms and suggesting experiments."

The Agent Generator will conduct an interview to understand:

- What specific immunology topics you work with
- What level of detail you prefer
- What types of tasks you'll perform (literature review, experimental design, data analysis)
- What output formats work best for you

After the interview, it generates an agent configuration for your approval.

### Using Generated Agents

Once created, agents are available at `~/.claude/agents/<agent-name>/` with:

- `config.yaml` - Full agent configuration including system prompt
- `feedback.jsonl` - Accumulated feedback data
- `versions.jsonl` - Change history

### Providing Feedback

After using an agent, provide structured feedback:

"The agent provided a clear explanation but missed recent papers from 2024. Rating: 4/5."

This feedback is automatically logged and used for future refinements.

## Workflow Examples

### Domain Expert Agent

**Request:** "Create a molecular biologist agent for CRISPR experimental design."

**Interview Questions:**
- What CRISPR systems do you work with? (SpCas9, base editing, prime editing?)
- What organisms? (Mammalian cells, bacteria, plants?)
- What deliverables? (gRNA sequences, donor templates, protocols?)

**Generated Agent:**
- Specialized in your CRISPR system
- Tailored to your organism
- Outputs in your preferred format

### Iterative Improvement

**Initial Performance:** Agent achieves 3.5/5 average rating.

**Common Feedback Themes:**
- Protocols lack troubleshooting tips
- Missing alternative approaches
- Citations could be more comprehensive

**Refinement Applied:**
- Added troubleshooting section to system prompt
- Included "always suggest alternatives" constraint
- Expanded few-shot examples with better citations

**Improved Performance:** Rating increases to 4.3/5.

## Advanced Features

### Pattern Selection

The Agent Generator has pre-built patterns for common agent types:

- **Technical Domain:** Deep expertise, literature-focused
- **Creative Writing:** Emphasis on style, narrative, voice
- ** Data Analysis:** Statistical reasoning, visualization

You can request a specific pattern or let the meta-agent infer from your requirements.

### Version Control

Every agent change is tracked in `versions.jsonl`:

```json
{
  "version": 3,
  "timestamp": "2026-02-02T16:15:00Z",
  "changes": ["Added troubleshooting constraint", "Updated few-shot examples"],
  "trigger": "feedback_analysis"
}
```

This enables rollback if a refinement degrades performance.

### Multi-Agent Systems

You can create agents that work together:

- Literature review agent finds relevant papers
- Experimental design agent proposes studies
- Data analysis agent interprets results

The meta-agent can help design agent teams for complex workflows.

## Best Practices

1. **Be Specific During Interview:** The more detailed your requirements, the better the initial agent.

2. **Provide Consistent Feedback:** Regular feedback improves refinement quality.

3. **Wait for Sufficient Data:** Trigger refinement after 10+ feedback entries for reliable patterns.

4. **Review Refinements Carefully:** Human approval catches edge cases that automated analysis misses.

5. **Version Control for Safety:** All changes are tracked, so you can always revert.

## Troubleshooting

### Agent Performance Plateaus

If an agent stops improving after refinements:

- Check if feedback is consistent (conflicting feedback confuses analysis)
- Consider if the domain has evolved (new papers, new techniques)
- Try a more specific interview to redefine scope

### Meta-Agent Self-Improvement

The meta-agent improves its own generation strategies by analyzing patterns across all agents. This happens automatically when:

- 10+ agents have been generated
- 50+ feedback entries exist across all agents
- Performance trends show clear patterns

You can also trigger self-improvement manually: "Analyze your generation patterns and improve your strategies."

## Architecture Notes

This system uses prompt engineering only. No model fine-tuning occurs. All improvements come from:

- Better system prompts
- More relevant few-shot examples
- Refined constraints and capabilities
- Updated generation patterns

This approach is safer, more transparent, and easier to debug than model fine-tuning.
