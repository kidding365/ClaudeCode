# Prompt Hooks Extension

Pre and post-sampling hooks for enhancing prompts with domain context, validation, and automated improvements.

## Overview

This extension provides a framework for automatically enhancing Claude Code prompts through hooks that run before and after model sampling. Hooks can inject context, validate outputs, enforce formats, and trigger follow-up actions.

## Installation

Hooks are loaded from `.claude/hooks/` directory. Enable this extension:

```bash
mkdir -p .claude/hooks/pre_sampling .claude/hooks/post_sampling
cp -r .claude/extensions/prompt-hooks/examples/* .claude/hooks/
```

## Hook Types

### Pre-Sampling Hooks

Run before the model generates a response. Use for:
- Injecting domain-specific context
- Adding constraints and guidelines
- Modifying prompts based on conversation state
- Triggering external data fetches

### Post-Sampling Hooks

Run after the model generates a response. Use for:
- Validating output format
- Enforcing structured output
- Triggering follow-up actions
- Logging and analytics

## Hook Configuration

### YAML Format

```yaml
# .claude/hooks/pre_sampling/domain-context.yaml
name: domain-context
description: Inject domain-specific knowledge
trigger: pre_sampling
conditions:
  # When to activate this hook
  patterns:
    - "*security*"
    - "*auth*"
    - "*vulnerability*"
actions:
  # What to do when triggered
  - type: inject_context
    source: file
    path: .claude/context/security-guidelines.md
    
  - type: add_constraints
    constraints:
      - "Always cite OWASP references"
      - "Include severity ratings"
      - "Provide remediation steps"
```

### JSON Format

```json
{
  "name": "api-validator",
  "description": "Validate API responses",
  "trigger": "post_sampling",
  "conditions": {
    "toolCalls": ["Bash"],
    "patterns": ["curl*", "http*"]
  },
  "actions": [
    {
      "type": "validate_json",
      "schema": ".claude/schemas/api-response.json",
      "onFailure": "request_correction"
    }
  ]
}
```

## Included Hooks

### Pre-Sampling Hooks

#### 1. Domain Context Injector

Injects relevant documentation based on conversation topic:

```yaml
# .claude/hooks/pre_sampling/domain-context.yaml
name: domain-context
trigger: pre_sampling
conditions:
  keywords:
    - database
    - api
    - authentication
    - testing
actions:
  - type: detect_topic
    model: small
  - type: inject_file
    mapping:
      database: .claude/context/database-guidelines.md
      api: .claude/context/api-standards.md
      authentication: .claude/context/auth-guidelines.md
      testing: .claude/context/testing-requirements.md
```

#### 2. Project Standards Enforcer

Automatically injects project-specific coding standards:

```yaml
# .claude/hooks/pre_sampling/project-standards.yaml
name: project-standards
trigger: pre_sampling
conditions:
  toolRequests:
    - FileEdit
    - FileWrite
actions:
  - type: inject_context
    content: |
      ## Project Coding Standards
      
      ### TypeScript
      - Use strict mode
      - Prefer interfaces over types
      - No any types without justification
      - JSDoc for all public APIs
      
      ### Testing
      - Unit tests for all functions
      - Integration tests for APIs
      - >80% coverage required
      
      ### Git
      - Conventional commits
      - Small, focused PRs
      - Squash merge to main
```

#### 3. Security Policy Injector

Injects security requirements for sensitive operations:

```yaml
# .claude/hooks/pre_sampling/security-policy.yaml
name: security-policy
trigger: pre_sampling
conditions:
  patterns:
    - "*password*"
    - "*token*"
    - "*secret*"
    - "*credential*"
    - "*encrypt*"
    - "*auth*"
actions:
  - type: inject_context
    content: |
      ## Security Requirements
      
      When handling sensitive data:
      
      1. **Never log** passwords, tokens, or secrets
      2. **Use environment variables** for configuration
      3. **Encrypt at rest** using AES-256
      4. **Encrypt in transit** using TLS 1.3+
      5. **Validate all input** before processing
      6. **Use parameterized queries** to prevent injection
      7. **Implement rate limiting** on auth endpoints
      8. **Log access** (without sensitive data) for audit
      
      Reference: OWASP Top 10, CWE Top 25
```

#### 4. Conversation State Tracker

Maintains context across conversation turns:

```yaml
# .claude/hooks/pre_sampling/state-tracker.yaml
name: state-tracker
trigger: pre_sampling
conditions:
  turnNumber: ">3"
actions:
  - type: summarize_previous_turns
    maxTokens: 500
  - type: track_pending_tasks
    file: .claude/state/pending-tasks.json
  - type: inject_summary
    position: after_system_prompt
```

#### 5. External Data Fetcher

Fetches real-time data before responding:

```yaml
# .claude/hooks/pre_sampling/data-fetcher.yaml
name: data-fetcher
trigger: pre_sampling
conditions:
  patterns:
    - "*current version*"
    - "*latest*"
    - "*check if*"
    - "*verify*"
actions:
  - type: exec
    command: curl -s https://api.example.com/version
    storeIn: LATEST_VERSION
  - type: inject_context
    content: "Latest version: $LATEST_VERSION"
```

### Post-Sampling Hooks

#### 1. Structured Output Validator

Validates that responses match expected schema:

```yaml
# .claude/hooks/post_sampling/output-validator.yaml
name: output-validator
trigger: post_sampling
conditions:
  expectedFormat: json
actions:
  - type: validate_json
    schema: .claude/schemas/expected-output.json
    onError:
      - type: request_correction
        message: "Output must match schema. Please fix."
      - type: log_error
        destination: .claude/logs/validation-errors.log
```

#### 2. Code Quality Checker

Runs linters and formatters on generated code:

```yaml
# .claude/hooks/post_sampling/code-quality.yaml
name: code-quality
trigger: post_sampling
conditions:
  toolCalls:
    - FileEdit
    - FileWrite
actions:
  - type: exec
    command: eslint --fix $MODIFIED_FILES
    onFailure:
      - type: request_fix
        message: "Code has linting errors. Please fix."
  - type: exec
    command: prettier --write $MODIFIED_FILES
  - type: exec
    command: tsc --noEmit
    onFailure:
      - type: request_fix
        message: "TypeScript errors found. Please fix."
```

#### 3. Test Runner

Automatically runs tests after code changes:

```yaml
# .claude/hooks/post_sampling/test-runner.yaml
name: test-runner
trigger: post_sampling
conditions:
  modifiedFiles:
    - "*.ts"
    - "*.tsx"
    - "*.js"
    - "*.py"
actions:
  - type: exec
    command: npm test -- --findRelatedTests $MODIFIED_FILES
    timeout: 60s
    onFailure:
      - type: notify_user
        message: "Tests failed. Review and fix."
      - type: create_issue
        template: test-failure
```

#### 4. Documentation Generator

Auto-generates docs from code changes:

```yaml
# .claude/hooks/post_sampling/doc-generator.yaml
name: doc-generator
trigger: post_sampling
conditions:
  modifiedFiles:
    - "src/**/*.ts"
actions:
  - type: skill
    name: /generate-api-docs
    args: "--incremental --files=$MODIFIED_FILES"
  - type: commit
    message: "docs: auto-update API documentation"
    files:
      - "docs/api/**"
```

#### 5. Change Summarizer

Creates summaries of changes for changelog:

```yaml
# .claude/hooks/post_sampling/change-summarizer.yaml
name: change-summarizer
trigger: post_sampling
conditions:
  sessionEnd: true
  hasFileChanges: true
actions:
  - type: exec
    command: git diff --stat HEAD
    storeIn: CHANGE_SUMMARY
  - type: append_file
    path: .claude/drafts/changelog-entry.md
    content: |
      ## $(date +%Y-%m-%d)
      
      Changes:
      $CHANGE_SUMMARY
```

## Advanced Hook Patterns

### Conditional Execution

```yaml
name: conditional-hook
trigger: pre_sampling
conditions:
  all:
    - patterns: ["*database*"]
    - timeOfDay: "business_hours"
    - dayOfWeek: ["monday", "tuesday", "wednesday", "thursday", "friday"]
actions:
  - type: inject_context
    path: .claude/context/db-guidelines.md
```

### Chained Actions

```yaml
name: chained-actions
trigger: post_sampling
conditions:
  toolCalls: ["FileEdit"]
actions:
  - type: exec
    command: git add $MODIFIED_FILES
  - type: exec
    command: npm test
  - type: conditional
    if: "$TEST_STATUS == 0"
    then:
      - type: exec
        command: git commit -m "auto: changes with passing tests"
    else:
      - type: notify_user
        message: "Tests failed, commit skipped"
```

### Multi-Hook Coordination

```yaml
# .claude/hooks/orchestration.yaml
hooks:
  - name: security-check
    trigger: pre_sampling
    priority: 1  # Run first
    conditions:
      patterns: ["*auth*", "*password*"]
    
  - name: context-injector
    trigger: pre_sampling
    priority: 2
    dependsOn: ["security-check"]
    
  - name: output-validator
    trigger: post_sampling
    priority: 1
    
  - name: test-runner
    trigger: post_sampling
    priority: 2
    dependsOn: ["output-validator"]
```

## Hook Actions Reference

| Action Type | Description | Parameters |
|-------------|-------------|------------|
| `inject_context` | Add text to prompt | `content`, `path`, `position` |
| `exec` | Run shell command | `command`, `timeout`, `storeIn` |
| `skill` | Invoke a skill | `name`, `args` |
| `validate_json` | Validate JSON output | `schema`, `onFailure` |
| `notify_user` | Send user notification | `message`, `channel` |
| `append_file` | Append to file | `path`, `content` |
| `commit` | Git commit | `message`, `files` |
| `request_correction` | Ask model to fix | `message` |
| `log` | Write to log | `destination`, `format` |
| `conditional` | Conditional execution | `if`, `then`, `else` |

## Context Files

Create context files to inject via hooks:

```markdown
# .claude/context/api-standards.md

## API Design Standards

### REST Conventions
- Use nouns for resources: `/users`, not `/getUsers`
- Use HTTP methods correctly: GET, POST, PUT, DELETE
- Return appropriate status codes
- Version APIs: `/api/v1/users`

### Response Format
```json
{
  "data": { ... },
  "meta": {
    "page": 1,
    "total": 100
  },
  "errors": []
}
```

### Error Handling
- Use problem+json format
- Include error codes
- Provide helpful messages
- Log errors server-side
```

## Debugging Hooks

### View Active Hooks

```bash
# List all configured hooks
claude hooks list

# Show hook execution order
claude hooks show-order

# Test hook conditions
claude hooks test-condition --hook=name --pattern="test pattern"
```

### Hook Logs

```bash
# View hook execution logs
claude logs hooks

# Filter by hook name
claude logs hooks --name=security-policy

# Real-time hook monitoring
claude logs hooks --follow
```

### Debug Mode

Enable verbose hook debugging:

```json
{
  "hooks": {
    "debug": true,
    "logExecutions": true,
    "dryRun": false
  }
}
```

## Best Practices

### Writing Effective Hooks

1. **Single Responsibility**: One concern per hook
2. **Clear Conditions**: Precise triggering criteria
3. **Fast Execution**: Keep hooks under 5 seconds
4. **Graceful Failures**: Don't block on non-critical hooks
5. **Idempotent Actions**: Safe to run multiple times
6. **Document Behavior**: Comment hook purposes

### Performance Considerations

```yaml
# Good: Parallel independent actions
actions:
  - type: exec
    command: lint &
  - type: exec
    command: typecheck &
  - type: wait_all

# Bad: Sequential when parallel possible
actions:
  - type: exec
    command: lint
  - type: exec
    command: typecheck  # Waits for lint unnecessarily
```

### Security Considerations

1. **Sandbox Exec Commands**: Restrict command capabilities
2. **Validate External Data**: Sanitize fetched content
3. **Limit File Access**: Scope file operations
4. **Audit Hook Actions**: Log all modifications

## Troubleshooting

### Hook Not Triggering

1. Check condition patterns match
2. Verify hook is enabled in config
3. Review hook execution logs
4. Test conditions independently

### Hook Causing Errors

1. Enable debug mode for details
2. Check command paths and permissions
3. Verify file paths exist
4. Review timeout settings

### Performance Issues

1. Profile hook execution time
2. Parallelize independent actions
3. Cache frequently accessed data
4. Reduce hook frequency if needed

## License

MIT
