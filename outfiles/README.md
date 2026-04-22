# Claude Code Enhancement Suite

A comprehensive collection of skills and extensions for enhancing Claude Code with research coordination, parallel execution, automated documentation, and prompt optimization capabilities.

## 📁 Structure

```
outfiles/
├── skills/                    # Custom skill definitions (.md format)
│   ├── research-coordinator.md
│   ├── parallel-executor.md
│   ├── documentation-generator.md
│   ├── code-reviewer.md
│   ├── refactoring-assistant.md
│   ├── test-generator.md
│   ├── debug-assistant.md
│   ├── security-auditor.md
│   ├── performance-optimizer.md
│   ├── migration-assistant.md
│   └── architecture-designer.md
│
└── extensions/                # Extension packages
    ├── claude-code-enhancement.mcpb    # MCP server bundle
    └── claude-code-enhancement-suite.dxt  # Complete extension suite
```

---

## 🎯 Skills

Skills are reusable prompt-based workflows that extend Claude Code's capabilities. Follow the format at [Claude Skills Documentation](https://support.claude.com/en/articles/12512198-how-to-create-custom-skills).

### Installing Skills

1. **Copy to skills directory:**
   ```bash
   cp outfiles/skills/*.md ~/.claude/skills/
   ```

2. **Or use the skill command:**
   ```bash
   claude skill install outfiles/skills/research-coordinator.md
   ```

3. **Enable a skill:**
   ```bash
   /skill enable research-coordinator
   ```

### Available Skills

| Skill | Trigger | Description |
|-------|---------|-------------|
| **Research Coordinator** | `/research` | Multi-agent research with specialized workers |
| **Parallel Executor** | `/parallel` | Concurrent task execution with coordination |
| **Documentation Generator** | `/docs` | Auto-generate API docs, READMEs, changelogs |
| **Code Reviewer** | `/review` | Security audits and quality checks |
| **Refactoring Assistant** | `/refactor` | Safe, incremental code improvements |
| **Test Generator** | `/test` | Unit, integration, and E2E test creation |
| **Debug Assistant** | `/debug` | Systematic bug diagnosis and fixes |
| **Security Auditor** | `/security` | OWASP compliance and vulnerability scanning |
| **Performance Optimizer** | `/optimize` | Bottleneck identification and optimization |
| **Migration Assistant** | `/migrate` | Framework/language migration support |
| **Architecture Designer** | `/architecture` | System design and tech stack recommendations |

---

## 📦 Extensions

### MCP Bundle (.mcpb)

The `claude-code-enhancement.mcpb` file contains Model Context Protocol servers:

- **Research Coordinator MCP**: Tools for spawning research workers and collecting results
- **Parallel Executor MCP**: Parallel task creation and monitoring
- **Documentation Generator MCP**: API doc generation and validation
- **Prompt Enhancer MCP**: Prompt optimization and output validation

#### Installing MCP Servers

```bash
# Install from bundle
claude mcp install outfiles/extensions/claude-code-enhancement.mcpb

# Or add individual servers
claude mcp add research-coordinator node path/to/research-coordinator.js
claude mcp add parallel-executor node path/to/parallel-executor.js
claude mcp add documentation-generator node path/to/documentation-generator.js
claude mcp add prompt-enhancer node path/to/prompt-enhancer.js
```

### DXT Extension Suite (.dxt)

The `claude-code-enhancement-suite.dxt` is a complete extension package including:

- All 11 skills
- 4 MCP servers
- 4 automation hooks
- 5 slash commands
- 4 output style templates

#### Installing the Extension Suite

```bash
claude extension install outfiles/extensions/claude-code-enhancement-suite.dxt
```

---

## 🔧 Usage Examples

### Research Coordination

```bash
# Start a research session
/research What are the latest developments in quantum error correction?

# The skill will:
# 1. Spawn literature reviewer and data analyst workers
# 2. Collect findings in parallel
# 3. Synthesize results into a structured report
```

### Parallel Execution

```bash
# Run tests in parallel
/parallel Run all test suites concurrently with coverage reporting

# Process files through a pipeline
/parallel Process these 100 files through validation → transformation → export
```

### Documentation Generation

```bash
# Generate API documentation
/docs Generate API documentation for the entire project

# Update README
/docs Update README with new installation instructions

# Create an ADR
/docs Create an ADR for using PostgreSQL over MongoDB
```

### Code Review

```bash
# Security audit
/review Audit this authentication module for vulnerabilities

# Performance analysis
/review Analyze performance of the data processing pipeline
```

---

## ⚙️ Configuration

Add to your `~/.claude/config.json`:

```json
{
  "enhancements": {
    "research": {
      "defaultWorkers": ["literature_reviewer", "data_analyst"],
      "maxConcurrent": 5
    },
    "documentation": {
      "autoUpdate": false,
      "outputFormat": "markdown"
    },
    "hooks": {
      "enablePreSampling": true,
      "enablePostSampling": true
    },
    "models": {
      "preferredModel": "claude-sonnet-4-20250514"
    }
  }
}
```

---

## 🪝 Hooks

Hooks automate actions before/after sampling or tasks:

| Hook | Type | Description |
|------|------|-------------|
| `pre-sampling-enhancer` | pre_sampling | Enhance prompts with domain context |
| `post-sampling-validator` | post_sampling | Validate output against schemas |
| `pre-commit-docs` | pre_commit | Auto-generate docs before commits |
| `post-task-analyzer` | post_task | Analyze completed tasks |

Enable hooks in config or via `/hooks enable <hook-name>`

---

## 📋 Output Formats

Skills produce structured outputs:

### Research Report
```markdown
# Research Summary

## Question
[Restated research question]

## Methodology
[Workers used and their roles]

## Findings
[Synthesized results]

## Hypotheses
[Generated hypotheses with confidence]

## References
[Cited sources]
```

### Code Review Report
```markdown
# Code Review Report

## Summary
- Files reviewed: X
- Issues: 🔴 X, 🟠 X, 🟡 X, 🟢 X

## Critical Issues
### [Issue]
- Location: `file:line`
- Fix: [solution]
```

---

## 🚀 Quick Start

1. **Install skills:**
   ```bash
   cp outfiles/skills/*.md ~/.claude/skills/
   ```

2. **Install MCP servers:**
   ```bash
   claude mcp install outfiles/extensions/claude-code-enhancement.mcpb
   ```

3. **Try your first skill:**
   ```bash
   /research Explain the current state of LLM reasoning techniques
   ```

---

## 📚 Additional Resources

- [Claude Skills Documentation](https://support.claude.com/en/articles/12512198-how-to-create-custom-skills)
- [MCP Specification](https://modelcontextprotocol.io/)
- [Claude Code Documentation](https://docs.anthropic.com/claude-code/)

---

## 📄 License

MIT License - See LICENSE file for details.
