# Claude Code Enhancement Framework

A comprehensive collection of extensions to improve Claude Code's capabilities for research, parallelization, documentation, skills, and prompt engineering.

## Overview

This framework provides 5 integrated extensions:

| Extension | Purpose | Key Features |
|-----------|---------|--------------|
| **Research Coordinator** | Tunnel research direction | Specialized worker types, hypothesis validation, structured output |
| **Parallel Tools** | Improve parallelization | Agent swarms, scheduled tasks, remote triggers, monitoring |
| **Documentation Plugin** | Auto-generate docs | API docs, changelogs, ADRs, validation |
| **Skill Templates** | Reusable workflows | Code review, debugging, testing, refactoring skills |
| **Prompt Hooks** | Improve prompts | Pre/post-sampling hooks, context injection, validation |

## Quick Start

### 1. Install All Extensions

```bash
# Copy all extensions to your .claude directory
cp -r .claude/extensions/* ~/.claude/

# Or symlink for development
ln -s $(pwd)/.claude/extensions/* ~/.claude/
```

### 2. Enable Plugins

Add to `.claude/settings.json`:

```json
{
  "enabledPlugins": {
    "research-coordinator@builtin": true,
    "parallel-tools@builtin": true,
    "docs-plugin@builtin": true
  },
  "coordinator": {
    "presets": {
      "research": {
        "enabled": true,
        "scratchpadDir": ".claude/research-scratch"
      }
    }
  }
}
```

### 3. Set Up Hooks

```bash
# Create hook directories
mkdir -p .claude/hooks/pre_sampling .claude/hooks/post_sampling

# Copy example hooks
cp .claude/extensions/prompt-hooks/examples/* .claude/hooks/
```

### 4. Load Skills

```bash
# Copy skill templates
cp -r .claude/extensions/skill-templates/templates/* .claude/skills/
```

## Extension Details

### 1. Research Coordinator

**Purpose**: Tunnel research direction with specialized agents and structured methodologies.

**Key Features**:
- 4 specialized worker types (Literature Reviewer, Data Analyst, Hypothesis Generator, Verifier)
- Parallel research stream orchestration
- Cross-worker knowledge sharing via scratchpad
- Structured hypothesis validation with confidence scores
- Auto-generated research summaries

**Usage Example**:
```bash
# Start research session
/research-coordinator start --topic="Session management vulnerabilities"

# Launches parallel workers for literature review, code analysis, and hypothesis generation
```

[Full Documentation](./research-coordinator/README.md)

---

### 2. Parallel Tools

**Purpose**: Maximize parallelization with agent swarms, scheduling, and event-driven workflows.

**Key Features**:
- Concurrent agent swarms with coordinated tool access
- Cron-based scheduled tasks
- Webhook-driven remote triggers
- Background MCP monitoring servers
- Dream tasks for speculative exploration

**Usage Example**:
```bash
# Schedule daily security scan
claude cron add "daily-security" "0 2 * * *" --skill="/security-scan"

# Launch agent swarm
TEAM_CREATE_TOOL({name: "audit-swarm", agents: [...]})
```

[Full Documentation](./parallel-tools/README.md)

---

### 3. Documentation Plugin

**Purpose**: Auto-generate and maintain comprehensive project documentation.

**Key Features**:
- `/generate-api-docs` - Extract API docs from source
- `/update-readme` - Keep README current
- `/generate-changelog` - Auto-changelog from commits
- `/create-adr` - Architecture decision records
- `/validate-docs` - Link checking, freshness monitoring
- `/export-docs` - Multi-format export (MD, HTML, PDF)

**Usage Example**:
```bash
# Generate API documentation
/generate-api-docs --source="src/**/*.ts" --output="docs/api"

# Validate all documentation
/validate-docs --check-links --check-code-examples
```

[Full Documentation](./doc-plugin/README.md)

---

### 4. Skill Templates

**Purpose**: Pre-built reusable skills for common workflows.

**Included Skills**:
- `/review-pr` - Comprehensive PR reviews
- `/security-review` - OWASP-focused security audits
- `/refactor` - Safe refactoring with verification
- `/add-feature` - Feature implementation checklist
- `/debug-issue` - Systematic debugging methodology
- `/performance-audit` - Performance profiling
- `/write-tests` - Comprehensive test generation

**Usage Example**:
```bash
# Review a pull request
/review-pr https://github.com/org/repo/pull/123

# Security audit
/security-review src/auth/
```

[Full Documentation](./skill-templates/README.md)

---

### 5. Prompt Hooks

**Purpose**: Automatically enhance prompts with context, constraints, and validation.

**Hook Types**:
- **Pre-Sampling**: Inject context, add constraints, fetch data
- **Post-Sampling**: Validate output, run tests, generate docs

**Included Hooks**:
- Domain context injector
- Project standards enforcer
- Security policy injector
- Output validator
- Code quality checker
- Test runner

**Usage Example**:
```yaml
# .claude/hooks/pre_sampling/security.yaml
trigger: pre_sampling
conditions:
  patterns: ["*auth*", "*password*"]
actions:
  - type: inject_context
    path: .claude/context/security-guidelines.md
```

[Full Documentation](./prompt-hooks/README.md)

---

## Integration Examples

### Example 1: Automated Security Review Pipeline

Combines all 5 extensions for comprehensive security analysis:

```bash
# 1. Start research coordinator for security investigation
/research-coordinator start --topic="Authentication vulnerabilities"

# 2. Launch parallel workers (Parallel Tools)
AGENT_TOOL({description: "OWASP review", subagent_type: "literature_reviewer", ...})
AGENT_TOOL({description: "Code analysis", subagent_type: "data_analyst", ...})

# 3. Run security review skill (Skill Templates)
/security-review src/auth/

# 4. Hooks automatically inject security policies (Prompt Hooks)
# Pre-sampling hook adds OWASP guidelines
# Post-sampling hook validates findings format

# 5. Generate documentation (Documentation Plugin)
/generate-api-docs --source="src/auth/**"
/create-adr --title="Migrate to JWT authentication"
```

### Example 2: CI/CD Integration

GitHub Actions workflow using all extensions:

```yaml
name: Claude Code CI

on: [pull_request]

jobs:
  claude-review:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      # Use skill templates
      - name: Security Review
        run: claude skill run /security-review src/
      
      - name: Code Quality
        run: claude skill run /review-pr ${{ github.event.pull_request.number }}
      
      # Triggered by webhook (Parallel Tools)
      - name: Trigger Remote Analysis
        run: |
          curl -X POST $CLAUDE_WEBHOOK \
            -d '{"pr": ${{ github.event.pull_request.number }}}'
      
      # Generate docs (Documentation Plugin)
      - name: Update API Docs
        run: claude skill run /generate-api-docs
        
      # Hooks validate everything (Prompt Hooks)
      - name: Validate Documentation
        run: claude skill run /validate-docs
```

### Example 3: Research Workflow

Complete research tunneling workflow:

```bash
# Initialize research with coordinator
/research-coordinator init --topic="Microservices vs Monolith"

# Fan out parallel research streams
# Worker 1: Literature review
AGENT_TOOL({
  description: "Academic research",
  subagent_type: "literature_reviewer",
  prompt: "Search for papers on microservices tradeoffs..."
})

# Worker 2: Industry case studies
AGENT_TOOL({
  description: "Industry analysis", 
  subagent_type: "data_analyst",
  prompt: "Find case studies of migrations..."
})

# Worker 3: Generate hypotheses
AGENT_TOOL({
  description: "Hypothesis generation",
  subagent_type: "hypothesis_generator",
  prompt: "Synthesize findings into testable hypotheses..."
})

# Workers share findings via scratchpad
# .claude/research-scratch/literature_review.md
# .claude/research-scratch/case_studies.json
# .claude/research-scratch/hypotheses.md

# Verifier evaluates all hypotheses
AGENT_TOOL({
  description: "Hypothesis verification",
  subagent_type: "verifier",
  prompt: "Evaluate hypotheses against evidence..."
})

# Generate final report
/export-docs --formats="md,pdf" --output="research-report"
```

## Configuration Reference

### Global Settings

```json
{
  "extensions": {
    "researchCoordinator": {
      "enabled": true,
      "maxWorkers": 5,
      "scratchpadDir": ".claude/research-scratch"
    },
    "parallelTools": {
      "maxConcurrentAgents": 10,
      "enableScheduler": true,
      "enableRemoteTriggers": true
    },
    "docsPlugin": {
      "outputDir": "docs",
      "autoUpdate": true,
      "validateOnCommit": true
    },
    "hooks": {
      "enabled": true,
      "debug": false
    }
  }
}
```

### Environment Variables

```bash
# Coordinator mode
export CLAUDE_CODE_COORDINATOR_MODE=1

# Parallel tools
export CLAUDE_MAX_CONCURRENT_AGENTS=10

# Debug mode
export CLAUDE_HOOKS_DEBUG=1
```

## Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                    Claude Code Core                         │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  ┌──────────────────┐  ┌──────────────────┐                │
│  │   Research       │  │   Parallel       │                │
│  │   Coordinator    │  │   Tools          │                │
│  │                  │  │                  │                │
│  │ • Worker Types   │  │ • Agent Swarms   │                │
│  │ • Scratchpad     │  │ • Scheduling     │                │
│  │ • Hypothesis     │  │ • Triggers       │                │
│  └──────────────────┘  └──────────────────┘                │
│                                                             │
│  ┌──────────────────┐  ┌──────────────────┐                │
│  │   Documentation  │  │   Skill          │                │
│  │   Plugin         │  │   Templates      │                │
│  │                  │  │                  │                │
│  │ • API Docs       │  │ • Review Skills  │                │
│  │ • Changelog      │  │ • Debug Skills   │                │
│  │ • Validation     │  │ • Test Skills    │                │
│  └──────────────────┘  └──────────────────┘                │
│                                                             │
│  ┌──────────────────────────────────────────┐              │
│  │           Prompt Hooks                   │              │
│  │                                          │              │
│  │  Pre-Sampling        Post-Sampling       │              │
│  │  • Context Inject    • Output Validate   │              │
│  │  • Constraints       • Run Tests         │              │
│  │  • Data Fetch        • Generate Docs     │              │
│  └──────────────────────────────────────────┘              │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

## Best Practices

### Research Tunneling
1. Define clear research questions before starting
2. Use specialized worker types for different aspects
3. Share findings via scratchpad directory
4. Synthesize findings before directing follow-up work
5. Always include falsification criteria in hypotheses

### Parallelization
1. Start with 2-3 workers, scale gradually
2. Use read-only parallelism freely
3. Serialize write operations to same files
4. Set timeouts for long-running tasks
5. Monitor resource usage

### Documentation
1. Document as you code (same PR)
2. Generate docs from source where possible
3. Validate links and examples regularly
4. Keep changelog updated
5. Export to multiple formats

### Skills
1. One skill, one purpose
2. Clear argument hints
3. Restrict tools to minimum needed
4. Include checklists and examples
5. Test skills like code

### Hooks
1. Single responsibility per hook
2. Fast execution (<5 seconds)
3. Graceful failure handling
4. Idempotent actions
5. Log for debugging

## Troubleshooting

### Common Issues

| Issue | Solution |
|-------|----------|
| Workers not sharing context | Check scratchpadDir config |
| Tasks not starting | Verify maxConcurrentAgents limit |
| Hook not triggering | Check condition patterns |
| Skill not found | Ensure file is in .claude/skills/ |
| Docs validation fails | Run with --fix flag |

### Debug Commands

```bash
# List active extensions
claude extensions list

# Show hook execution log
claude logs hooks --follow

# View running tasks
claude tasks list

# Check skill registration
claude skills list

# Validate configuration
claude doctor
```

## Contributing

To contribute new features:

1. Create extension in `.claude/extensions/`
2. Add comprehensive documentation
3. Include example configurations
4. Test with real workflows
5. Submit for review

## Version Compatibility

| Extension | Min Claude Code Version |
|-----------|------------------------|
| Research Coordinator | 1.0.0+ |
| Parallel Tools | 1.0.0+ |
| Documentation Plugin | 1.0.0+ |
| Skill Templates | 1.0.0+ |
| Prompt Hooks | 1.0.0+ |

## License

MIT

## Support

For issues and feature requests, please open an issue on the repository.
