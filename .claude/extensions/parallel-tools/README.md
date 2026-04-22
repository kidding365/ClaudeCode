# Parallel Tools Extension

Enables advanced parallelization patterns for Claude Code including scheduled tasks, remote triggers, and concurrent workflow orchestration.

## Overview

This extension provides infrastructure for running multiple Claude Code operations in parallel with coordination, scheduling, and event-driven triggers.

## Features

- **Concurrent Agent Swarms**: Launch multiple workers with coordinated tool access
- **Scheduled Tasks**: Cron-based recurring research/analysis jobs
- **Remote Triggers**: Webhook-driven task initiation
- **Monitor MCP Servers**: Background monitoring with alerting
- **Dream Tasks**: Async speculative execution for exploration

## Installation

Enable the parallel tools plugin:

```bash
claude plugin enable parallel-tools@builtin
```

Or add to `.claude/settings.json`:

```json
{
  "enabledPlugins": {
    "parallel-tools@builtin": true
  },
  "parallelTools": {
    "maxConcurrentAgents": 10,
    "enableScheduler": true,
    "enableRemoteTriggers": true
  }
}
```

## Components

### 1. Agent Swarm Coordinator

Launch coordinated agent swarms with shared context:

```typescript
// Launch a swarm of 5 agents working on different aspects
TEAM_CREATE_TOOL({
  name: "security-audit-swarm",
  agents: [
    { id: "scanner", role: "vulnerability_scanner" },
    { id: "analyzer", role: "pattern_analyzer" },
    { id: "reporter", role: "report_generator" },
    { id: "verifier", role: "fix_verifier" },
    { id: "coordinator", role: "swarm_coordinator" }
  ]
})

// Agents communicate via SEND_MESSAGE_TOOL
SEND_MESSAGE_TOOL({
  to: "scanner",
  message: "Scan complete. Found 3 potential issues. Passing to analyzer."
})
```

### 2. Scheduled Tasks (Cron)

Schedule recurring analysis, documentation updates, or maintenance:

```bash
# Daily security scan at 2 AM
claude cron add "daily-security-scan" "0 2 * * *" --skill="/security-scan"

# Weekly documentation update
claude cron add "weekly-docs" "0 9 * * 1" --prompt="Update API docs from source code"

# Hourly health check
claude cron add "health-check" "0 * * * *" --skill="/health-monitor"
```

**Configuration:**

```json
{
  "scheduledTasks": {
    "daily-security-scan": {
      "schedule": "0 2 * * *",
      "type": "skill",
      "target": "/security-scan",
      "notifyOnComplete": true,
      "maxDuration": "30m"
    },
    "weekly-docs": {
      "schedule": "0 9 * * 1",
      "type": "prompt",
      "prompt": "Generate documentation from current codebase state",
      "outputFile": "docs/auto-generated.md"
    }
  }
}
```

### 3. Remote Triggers

Trigger Claude Code tasks via webhooks or external events:

```bash
# Create a trigger endpoint
claude trigger create "pr-review" --endpoint="/webhooks/pr-review"

# Trigger with payload
curl -X POST http://localhost:8080/webhooks/pr-review \
  -H "Content-Type: application/json" \
  -d '{"pr_number": 123, "repo": "my-org/my-repo"}'
```

**Use Cases:**
- GitHub PR reviews on push
- CI/CD pipeline integration
- Slack command triggers
- Monitoring alert responses

### 4. Monitor MCP Servers

Background monitoring servers that watch for conditions and alert:

```json
{
  "mcpServers": {
    "monitor-ci": {
      "command": "npx -y @claude/mcp-monitor-ci",
      "transport": "stdio",
      "config": {
        "watchRepos": ["my-org/repo1", "my-org/repo2"],
        "checkInterval": "60s",
        "alerts": ["ci_failure", "merge_conflict"]
      }
    },
    "monitor-deps": {
      "command": "npx -y @claude/mcp-monitor-deps",
      "transport": "stdio", 
      "config": {
        "packageFiles": ["package.json", "requirements.txt"],
        "checkSecurity": true,
        "notifyUpdates": true
      }
    }
  }
}
```

### 5. Dream Tasks

Speculative async tasks for exploration without blocking:

```typescript
// Start a dream task for exploratory research
DREAM_TASK({
  description: "Explore alternative architectures",
  prompt: "Research microservices vs monolith for this use case. No action needed - just explore and document findings.",
  priority: "low",
  notifyOnCompletion: true
})

// Dream tasks run in background, notify when complete
```

## Parallelization Patterns

### Pattern 1: Fan-Out Research

```typescript
// Fan out to multiple specialized researchers
const topics = ["auth", "database", "api", "frontend"];

topics.forEach(topic => {
  AGENT_TOOL({
    description: `Research ${topic} patterns`,
    subagent_type: "researcher",
    prompt: `Deep dive into ${topic}. Find all relevant files, patterns, and potential issues.`
  });
});

// All run in parallel, results arrive as notifications
```

### Pattern 2: Pipeline Processing

```typescript
// Sequential pipeline with parallel stages
// Stage 1: Parallel data collection
AGENT_TOOL({ description: "Collect logs", ... });
AGENT_TOOL({ description: "Collect metrics", ... });
AGENT_TOOL({ description: "Collect traces", ... });

// Stage 2: Parallel analysis (after stage 1 completes)
// triggered by task notifications
SEND_MESSAGE_TOOL({ to: "log-analyzer", message: "Analyze collected logs" });
SEND_MESSAGE_TOOL({ to: "metrics-analyzer", message: "Analyze metrics" });

// Stage 3: Synthesis
AGENT_TOOL({ description: "Synthesize findings", ... });
```

### Pattern 3: Map-Reduce

```typescript
// Map: Process files in parallel
const files = await glob("**/*.ts");
files.forEach(file => {
  AGENT_TOOL({
    description: `Analyze ${file}`,
    prompt: `Analyze ${file} for code quality issues.`
  });
});

// Reduce: Synthesize all analyses
// Triggered after all map tasks complete
AGENT_TOOL({
  description: "Consolidate findings",
  prompt: "Combine all file analyses into a unified report."
});
```

### Pattern 4: Competing Hypotheses

```typescript
// Generate multiple competing hypotheses in parallel
AGENT_TOOL({
  description: "Hypothesis: Auth bug is timing-related",
  prompt: "Investigate if the auth bug could be caused by race conditions or timing issues."
});

AGENT_TOOL({
  description: "Hypothesis: Auth bug is data-related", 
  prompt: "Investigate if the auth bug could be caused by malformed input or data corruption."
});

AGENT_TOOL({
  description: "Hypothesis: Auth bug is config-related",
  prompt: "Investigate if the auth bug could be caused by misconfiguration."
});

// Verifier evaluates all hypotheses
AGENT_TOOL({
  description: "Evaluate hypotheses",
  prompt: "Review all three hypothesis reports. Determine which is most likely and why."
});
```

## Configuration Reference

### Global Settings

```json
{
  "parallelTools": {
    "maxConcurrentAgents": 10,
    "maxConcurrentWorkflows": 5,
    "defaultTimeout": "30m",
    "scratchpadDir": ".claude/parallel-scratch",
    "enableNotifications": true,
    "logLevel": "info"
  }
}
```

### Task Types

| Type | Description | Use Case |
|------|-------------|----------|
| `local_workflow` | Orchestrates multiple subtasks | Complex multi-step processes |
| `monitor_mcp` | Background monitoring | Continuous observation |
| `dream` | Speculative async tasks | Exploration without blocking |
| `cron` | Scheduled recurring tasks | Periodic maintenance |
| `remote_trigger` | Webhook-triggered tasks | External integrations |

### Concurrency Controls

```json
{
  "concurrency": {
    "readOnlyTasks": { "limit": 20, "strategy": "unlimited" },
    "writeTasks": { "limit": 3, "strategy": "sequential-per-path" },
    "verificationTasks": { "limit": 5, "strategy": "parallel" },
    "networkTasks": { "limit": 10, "strategy": "rate-limited" }
  }
}
```

## Integration Examples

### GitHub Actions Integration

```yaml
# .github/workflows/claude-review.yml
name: Claude Code Review
on: [pull_request]

jobs:
  review:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Trigger Claude Review
        run: |
          curl -X POST ${{ secrets.CLAUDE_WEBHOOK_URL }} \
            -H "Content-Type: application/json" \
            -d '{
              "event": "pr_opened",
              "pr_number": ${{ github.event.pull_request.number }},
              "repo": "${{ github.repository }}"
            }'
```

### Slack Integration

```javascript
// Slack app handler for claude commands
app.command('/claude', async ({ command, ack, say }) => {
  await ack();
  
  const [action, ...args] = command.text.split(' ');
  
  await fetch('http://localhost:8080/webhooks/slack', {
    method: 'POST',
    body: JSON.stringify({
      trigger: action,
      args: args.join(' '),
      user: command.user_id,
      channel: command.channel_id
    })
  });
});
```

### VS Code Integration

```json
// .vscode/tasks.json
{
  "version": "2.0.0",
  "tasks": [
    {
      "label": "Claude: Security Scan",
      "type": "shell",
      "command": "claude task create security-scan",
      "problemMatcher": []
    },
    {
      "label": "Claude: Generate Docs",
      "type": "shell", 
      "command": "claude skill run /generate-docs",
      "problemMatcher": []
    }
  ]
}
```

## Monitoring and Debugging

### View Running Tasks

```bash
# List all active tasks
claude tasks list

# View specific task details
claude tasks get <task-id>

# Stream task output
claude tasks follow <task-id>
```

### Logs and Metrics

```bash
# View parallel tools logs
claude logs parallel-tools

# Get concurrency metrics
claude stats parallel-tools

# Export task history
claude tasks export --format=json > tasks.json
```

## Best Practices

1. **Start Small**: Begin with 2-3 parallel workers, scale up gradually
2. **Use Scratchpad**: Share context between workers via scratchpad directory
3. **Set Timeouts**: Always configure max duration for long-running tasks
4. **Monitor Resources**: Watch CPU/memory usage with many concurrent agents
5. **Graceful Degradation**: Handle partial failures in fan-out patterns
6. **Idempotent Operations**: Make scheduled tasks safe to re-run

## Troubleshooting

### Tasks Not Starting
- Check `maxConcurrentAgents` limit not exceeded
- Verify MCP server connections are healthy
- Review permission settings for required tools

### Race Conditions
- Use scratchpad for inter-worker communication
- Implement file locking for shared resources
- Consider sequential strategy for write-heavy tasks

### Memory Issues
- Reduce `maxConcurrentAgents`
- Enable task output streaming instead of buffering
- Clean up scratchpad directory periodically

## License

MIT
