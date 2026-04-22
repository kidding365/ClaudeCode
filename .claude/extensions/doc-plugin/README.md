# Documentation Plugin

Auto-generates, maintains, and validates project documentation using Claude Code skills and hooks.

## Overview

This plugin provides comprehensive documentation automation including:
- Auto-generated API documentation from source code
- Architecture decision records (ADRs)
- Changelog generation from commits
- README maintenance
- Documentation validation and link checking
- Multi-format export (Markdown, HTML, PDF)

## Installation

```bash
claude plugin enable docs-plugin@builtin
```

Or add to `.claude/settings.json`:

```json
{
  "enabledPlugins": {
    "docs-plugin@builtin": true
  },
  "docsPlugin": {
    "outputDir": "docs",
    "autoUpdate": true,
    "validateOnCommit": true
  }
}
```

## Skills

### `/generate-api-docs`

Extracts API documentation from source code comments and type definitions.

**Usage:**
```bash
/generate-api-docs --source="src/**/*.ts" --output="docs/api"
```

**Features:**
- TypeScript/JavaScript JSDoc parsing
- Python docstring extraction
- Go doc comments
- Rust doc comments
- OpenAPI/Swagger generation
- Interactive documentation with examples

**Output Structure:**
```
docs/api/
├── index.md           # API overview
├── modules/           # Module documentation
├── classes/           # Class documentation
├── functions/         # Function documentation
├── types/             # Type definitions
└── examples/          # Usage examples
```

### `/update-readme`

Automatically updates README.md based on current project state.

**Usage:**
```bash
/update-readme --sections="installation,usage,api" --include-examples
```

**Updates:**
- Project description from package.json/pyproject.toml
- Installation instructions from actual setup files
- Usage examples from test files
- API summary from source code
- Badge updates (tests, coverage, version)
- Table of contents regeneration

### `/generate-changelog`

Creates changelog entries from git commits following conventional commits.

**Usage:**
```bash
/generate-changelog --from="v1.0.0" --to="HEAD" --output="CHANGELOG.md"
```

**Features:**
- Conventional commit parsing (feat, fix, chore, etc.)
- Automatic categorization (Features, Bug Fixes, Breaking Changes)
- PR and issue link extraction
- Contributor attribution
- Breaking change highlighting

### `/create-adr`

Generates Architecture Decision Records for significant technical decisions.

**Usage:**
```bash
/create-adr --title="Use PostgreSQL for primary datastore" --context="..." --decision="..."
```

**ADR Template:**
```markdown
# ADR-NNN: Title

## Status
Proposed | Accepted | Deprecated | Superseded

## Context
What is the issue that we're seeing that is motivating this decision?

## Decision
What is the change that we're proposing and/or doing?

## Consequences
What becomes easier or more difficult to do because of this change?
```

### `/validate-docs`

Validates documentation for broken links, outdated references, and quality issues.

**Usage:**
```bash
/validate-docs --check-links --check-code-examples --check-freshness
```

**Checks:**
- Broken internal/external links
- Outdated code examples (vs current API)
- Missing documentation for public APIs
- Stale screenshots/diagrams
- Inconsistent terminology
- Accessibility issues

### `/export-docs`

Exports documentation to multiple formats.

**Usage:**
```bash
/export-docs --formats="md,html,pdf" --output="dist/docs"
```

**Supported Formats:**
- Markdown (source)
- HTML (with navigation, search)
- PDF (printable documentation)
- ePub (e-book format)
- Docset (Dash/Zeppelin)

## Hooks

### Pre-Commit Documentation Check

Automatically validates documentation before commits:

```yaml
# .claude/hooks/pre_commit/docs.yaml
trigger: "pre_commit"
conditions:
  - filesChanged: ["**/*.md", "**/*.tsx", "**/*.py"]
actions:
  - skill: "/validate-docs"
  - block: true
  - suggestFixes: true
```

### Post-Merge Documentation Update

Updates documentation after merges to main:

```yaml
# .claude/hooks/post_merge/docs.yaml
trigger: "post_merge"
conditions:
  - branch: "main"
  - hasCodeChanges: true
actions:
  - skill: "/update-readme"
  - skill: "/generate-changelog"
  - commit: true
```

### Documentation Freshness Monitor

Periodically checks for stale documentation:

```yaml
# .claude/hooks/cron/docs-freshness.yaml
schedule: "0 9 * * 1"  # Weekly on Monday
actions:
  - skill: "/validate-docs --check-freshness"
  - notify: "#docs-channel"
  - createIssue: true
```

## MCP Integration

Connect documentation tools and platforms:

```json
{
  "mcpServers": {
    "notion": {
      "command": "npx -y @notion/mcp-server",
      "transport": "stdio",
      "config": {
        "databaseId": "your-db-id"
      }
    },
    "confluence": {
      "url": "https://your-org.atlassian.net/wiki",
      "transport": "sse",
      "auth": "basic"
    },
    "gitbook": {
      "command": "npx -y @gitbook/mcp",
      "transport": "stdio"
    },
    "docusaurus": {
      "command": "npx -y @docusaurus/mcp",
      "transport": "stdio"
    }
  }
}
```

## Configuration

### Global Settings

```json
{
  "docsPlugin": {
    "outputDir": "docs",
    "autoUpdate": true,
    "validateOnCommit": true,
    "linkCheckInterval": "7d",
    "codeExampleTests": true,
    "screenshotUpdateThreshold": "30d",
    "exportFormats": ["md", "html"],
    "notifyChannels": ["#docs-updates"]
  }
}
```

### Documentation Templates

Configure templates for different doc types:

```json
{
  "templates": {
    "api": {
      "header": "docs/templates/api-header.md",
      "exampleFormat": "typescript",
      "includeSourceLinks": true
    },
    "adr": {
      "template": "docs/templates/adr-template.md",
      "directory": "docs/adrs",
      "numbering": "sequential"
    },
    "changelog": {
      "format": "keepachangelog",
      "groupBy": "type",
      "includeContributors": true
    }
  }
}
```

## Workflows

### Workflow 1: Release Documentation

Complete documentation update for releases:

```bash
# Run release documentation workflow
/docs-workflow release --version="1.2.0"

# This executes:
# 1. Generate API docs from current code
# 2. Update CHANGELOG.md
# 3. Update README with new features
# 4. Create release-specific docs page
# 5. Validate all links and examples
# 6. Export to all configured formats
```

### Workflow 2: New Feature Documentation

Documentation workflow for new features:

```bash
# Trigger when new feature branch is created
/docs-workflow feature --branch="feature/new-auth"

# This:
# 1. Creates ADR for architectural decisions
# 2. Sets up API doc stubs
# 3. Adds feature to upcoming changelog
# 4. Creates documentation checklist
```

### Workflow 3: Documentation Audit

Quarterly documentation health check:

```bash
/docs-workflow audit --quarter="Q1-2024"

# Produces:
# - Documentation coverage report
# - Staleness analysis
# - Broken link summary
# - Improvement recommendations
# - Priority action items
```

## Quality Metrics

The plugin tracks documentation quality metrics:

| Metric | Description | Target |
|--------|-------------|--------|
| Coverage | % of public APIs documented | >95% |
| Freshness | Avg days since last update | <30 days |
| Link Health | % of valid links | 100% |
| Example Accuracy | % of code examples that compile/run | >90% |
| Readability | Flesch-Kincaid grade level | 8-12 |

View metrics:
```bash
/docs-metrics show
/docs-metrics trend --period="90d"
```

## Integration Examples

### GitHub Actions

```yaml
# .github/workflows/docs.yml
name: Documentation

on:
  push:
    branches: [main]
  pull_request:
    paths: ['**/*.md', 'src/**']

jobs:
  validate:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Validate Documentation
        run: claude skill run /validate-docs --ci-mode
      
      - name: Check Links
        run: claude skill run /validate-docs --check-links

  generate:
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'
    steps:
      - uses: actions/checkout@v4
      
      - name: Generate API Docs
        run: claude skill run /generate-api-docs
      
      - name: Deploy to GitHub Pages
        uses: peaceiris/actions-gh-pages@v4
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./docs
```

### VS Code Integration

```json
// .vscode/settings.json
{
  "docsPlugin.autoPreview": true,
  "docsPlugin.validateOnSave": true,
  "docsPlugin.quickCommands": {
    "updateApi": "claude skill run /generate-api-docs",
    "validateAll": "claude skill run /validate-docs"
  }
}
```

### CI/CD Pipeline

```yaml
# GitLab CI
docs:
  stage: test
  script:
    - claude skill run /validate-docs --fail-on-errors
    - claude skill run /generate-api-docs --verify-complete
  artifacts:
    paths:
      - docs/
    expire_in: 1 week
```

## Commands Reference

| Command | Description |
|---------|-------------|
| `/generate-api-docs` | Extract API docs from source |
| `/update-readme` | Update project README |
| `/generate-changelog` | Create changelog from commits |
| `/create-adr` | Generate architecture decision record |
| `/validate-docs` | Validate documentation quality |
| `/export-docs` | Export to multiple formats |
| `/docs-metrics` | Show documentation metrics |
| `/docs-workflow` | Run documentation workflows |

## Troubleshooting

### API Docs Generation Fails
- Ensure source files have proper JSDoc/docstring comments
- Check file patterns match existing files
- Verify TypeScript compilation succeeds first

### Link Check False Positives
- Add URLs to ignore list in config
- Configure authentication for private resources
- Increase timeout for slow external sites

### Code Examples Out of Sync
- Enable `codeExampleTests` in config
- Run `/validate-docs --check-code-examples` regularly
- Set up pre-commit hook for automatic validation

## Best Practices

1. **Document as You Code**: Update docs in the same PR as code changes
2. **Single Source of Truth**: Generate docs from code where possible
3. **Automate Validation**: Use hooks to catch issues early
4. **Version Documentation**: Keep docs versioned with releases
5. **Include Examples**: Every API should have usage examples
6. **Regular Audits**: Schedule quarterly documentation reviews

## License

MIT
