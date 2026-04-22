# Research Coordinator Extension

A specialized coordinator mode preset for research workflows with curated worker types and structured output enforcement.

## Overview

This extension enhances Claude Code's coordinator mode for research tasks by providing:
- Specialized researcher agent types with domain-specific tool access
- Parallel research stream orchestration
- Cross-worker knowledge sharing via scratchpad
- Structured hypothesis validation workflows
- Auto-generated literature review summaries

## Installation

Add to your `.claude/settings.json`:

```json
{
  "coordinator": {
    "presets": {
      "research": {
        "enabled": true,
        "scratchpadDir": ".claude/research-scratch",
        "workerTypes": ["literature_reviewer", "data_analyst", "hypothesis_generator", "verifier"]
      }
    }
  }
}
```

## Worker Types

### Literature Reviewer
- **Tools**: FileRead, Grep, Glob, MCP (academic databases)
- **Purpose**: Search codebase, documentation, and external sources for relevant information
- **Output**: Structured findings with citations

### Data Analyst  
- **Tools**: Bash (Python/R), FileRead, FileWrite, MCP (data sources)
- **Purpose**: Analyze data, run statistical tests, generate visualizations
- **Output**: Analysis reports with charts and statistical summaries

### Hypothesis Generator
- **Tools**: All read tools, SyntheticOutputTool
- **Purpose**: Synthesize findings into testable hypotheses
- **Output**: Structured hypotheses with evidence links

### Verifier
- **Tools**: Bash, FileRead, SyntheticOutputTool
- **Purpose**: Validate hypotheses against evidence
- **Output**: Verification results with confidence scores

## Usage

### Starting a Research Session

```
/research-coordinator start --topic="Your research question" --workers=4
```

### Parallel Research Streams

The coordinator automatically fans out research across multiple workers:

```typescript
// Example: Launch parallel research workers
AGENT_TOOL({
  description: "Review authentication patterns",
  subagent_type: "literature_reviewer",
  prompt: "Search for all auth-related files. Find patterns in token handling, session management, and OAuth flows. Report file paths, line numbers, and code snippets."
})

AGENT_TOOL({
  description: "Analyze security vulnerabilities", 
  subagent_type: "data_analyst",
  prompt: "Run static analysis on auth modules. Check for common vulnerabilities (XSS, CSRF, injection). Generate a risk report."
})
```

### Cross-Worker Knowledge Sharing

Workers share findings via the scratchpad directory:

```
.claude/research-scratch/
├── literature_review.md      # Compiled findings from reviewers
├── data_analysis.json        # Structured analysis results
├── hypotheses.md            # Generated hypotheses
├── verification_results.md   # Validation outcomes
└── synthesis.md             # Coordinator's final synthesis
```

## Structured Output Enforcement

All workers use `SyntheticOutputTool` to enforce structured responses:

```json
{
  "type": "research_findings",
  "findings": [
    {
      "source": "file/path.ts:42",
      "observation": "Description of finding",
      "evidence": "Code snippet or data point",
      "confidence": 0.95
    }
  ],
  "hypotheses": [
    {
      "statement": "Testable hypothesis",
      "supporting_evidence": ["source1", "source2"],
      "falsification_criteria": "What would disprove this"
    }
  ]
}
```

## Hooks

### Pre-Sampling Hook
Injects domain-specific context and research constraints:

```yaml
# .claude/hooks/pre_sampling/research.yaml
trigger: "research"
action: |
  - Inject citation format requirements
  - Add domain-specific search terms
  - Set evidence quality thresholds
```

### Post-Sampling Hook
Validates research output structure:

```yaml
# .claude/hooks/post_sampling/research.yaml
trigger: "research_findings"
action: |
  - Verify all claims have citations
  - Check confidence scores are justified
  - Flag unsupported assertions
```

## MCP Integration

Connect academic databases and research tools:

```json
{
  "mcpServers": {
    "semantic-scholar": {
      "command": "npx -y @anthropic/mcp-server-semantic-scholar",
      "transport": "stdio"
    },
    "arxiv": {
      "url": "http://localhost:8080/sse",
      "transport": "sse"
    },
    "github-research": {
      "command": "gh mcp server",
      "transport": "stdio"
    }
  }
}
```

## Example Workflow

### 1. Initialize Research

```
/research-coordinator init --topic="Session management vulnerabilities"
```

### 2. Launch Parallel Workers

```
You: Starting research on session management vulnerabilities.

AGENT_TOOL({description: "Literature review", subagent_type: "literature_reviewer", ...})
AGENT_TOOL({description: "Code analysis", subagent_type: "data_analyst", ...})
AGENT_TOOL({description: "Generate hypotheses", subagent_type: "hypothesis_generator", ...})

Research initiated across 3 parallel streams.
```

### 3. Receive Worker Results

```xml
<task-notification>
<task-id>lit-review-001</task-id>
<status>completed</status>
<summary>Literature review completed with 15 relevant findings</summary>
<result>{"type":"research_findings","findings":[...],...}</result>
</task-notification>
```

### 4. Synthesize and Verify

Coordinator synthesizes findings and dispatches verifiers:

```
SEND_MESSAGE_TOOL({to: "hypothesis-gen-001", message: "Refine hypothesis H3 based on literature findings L7, L12. Add falsification criteria."})

AGENT_TOOL({description: "Verify H3", subagent_type: "verifier", ...})
```

### 5. Final Report

Auto-generated research summary in `.claude/research-scratch/synthesis.md`.

## Configuration Options

| Option | Type | Default | Description |
|--------|------|---------|-------------|
| `maxWorkers` | number | 5 | Maximum concurrent workers |
| `scratchpadEnabled` | boolean | true | Enable cross-worker knowledge sharing |
| `structuredOutputRequired` | boolean | true | Enforce JSON output schema |
| `citationFormat` | string | "APA" | Citation format for literature reviews |
| `minConfidenceScore` | number | 0.7 | Minimum confidence for hypotheses |

## Troubleshooting

### Workers Not Sharing Context
- Ensure `scratchpadDir` is configured
- Check write permissions on scratchpad directory
- Verify workers are using the correct base directory

### Structured Output Failures
- Check `SyntheticOutputTool` is in allowed tools
- Verify JSON schema matches expected format
- Review hook enforcement logs

### MCP Connection Issues
- Run `claude mcp list` to verify connections
- Check server logs for transport errors
- Ensure OAuth tokens are valid

## Contributing

To add new worker types or research templates:

1. Create worker definition in `.claude/extensions/research-coordinator/workers/`
2. Add prompt templates in `.claude/extensions/research-coordinator/templates/`
3. Register hooks in `.claude/hooks/`

## License

MIT
