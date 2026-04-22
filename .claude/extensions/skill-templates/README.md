# Skill Templates

Reusable skill definitions and templates for common Claude Code workflows.

## Overview

This extension provides a library of pre-built skills that can be customized and extended for your specific needs. Skills are reusable prompt-based workflows that encapsulate best practices for common tasks.

## Installation

Skills are automatically loaded from `.claude/skills/` directory. Copy templates from this extension:

```bash
cp -r .claude/extensions/skill-templates/templates/* .claude/skills/
```

## Included Skill Templates

### 1. Code Review Skills

#### `/review-pr` - Pull Request Review

```yaml
# .claude/skills/review-pr.yaml
name: review-pr
description: Comprehensive pull request review
argumentHint: "<PR number or URL>"
whenToUse: "Reviewing code changes before merge"
allowedTools:
  - Bash
  - FileRead
  - Grep
  - Glob
model: claude-sonnet-4-20250514

prompt: |
  Review pull request $ARGUMENTS with the following checklist:
  
  ## Code Quality
  - [ ] Code follows project style guidelines
  - [ ] No obvious bugs or edge cases missed
  - [ ] Error handling is appropriate
  - [ ] Code is well-organized and readable
  
  ## Testing
  - [ ] Tests cover new functionality
  - [ ] Existing tests still pass
  - [ ] Edge cases are tested
  
  ## Security
  - [ ] No security vulnerabilities introduced
  - [ ] Input validation is adequate
  - [ ] No sensitive data exposure
  
  ## Documentation
  - [ ] Code comments explain complex logic
  - [ ] README/docs updated if needed
  - [ ] API changes documented
  
  ## Process
  - [ ] Commit messages are clear
  - [ ] PR description explains the change
  - [ ] Related issues linked
  
  Provide specific feedback with file:line references for any issues found.
```

#### `/security-review` - Security Audit

```yaml
# .claude/skills/security-review.yaml
name: security-review
description: Security-focused code review
argumentHint: "[directory or file pattern]"
whenToUse: "Security auditing of code"
allowedTools:
  - Bash
  - FileRead
  - Grep
  - Glob
  - MCP

prompt: |
  Perform a security audit of $ARGUMENTS focusing on:
  
  ### OWASP Top 10
  1. Injection vulnerabilities (SQL, command, LDAP)
  2. Broken authentication
  3. Sensitive data exposure
  4. XML External Entities (XXE)
  5. Broken access control
  6. Security misconfiguration
  7. Cross-Site Scripting (XSS)
  8. Insecure deserialization
  9. Vulnerable components
  10. Insufficient logging/monitoring
  
  ### Additional Checks
  - Hardcoded credentials or secrets
  - Insecure cryptographic usage
  - Race conditions
  - Path traversal vulnerabilities
  - SSRF possibilities
  
  For each finding, provide:
  - Severity (Critical/High/Medium/Low)
  - Location (file:line)
  - Description
  - Remediation suggestion
  - CVSS score estimate
```

### 2. Development Workflow Skills

#### `/refactor` - Safe Refactoring

```yaml
# .claude/skills/refactor.yaml
name: refactor
description: Safe code refactoring with verification
argumentHint: "<what to refactor> in <where>"
whenToUse: "Improving code structure without changing behavior"
allowedTools:
  - Bash
  - FileRead
  - FileEdit
  - Glob
  - Grep

prompt: |
  Refactor $ARGUMENTS following these steps:
  
  ## Phase 1: Analysis
  1. Understand current implementation
  2. Identify all call sites and dependencies
  3. Check for existing tests
  4. Document expected behavior
  
  ## Phase 2: Plan
  1. Describe refactoring approach
  2. List files that will change
  3. Identify potential risks
  4. Plan verification strategy
  
  ## Phase 3: Execute
  1. Make changes incrementally
  2. Run tests after each change
  3. Update related code as needed
  4. Keep commits small and focused
  
  ## Phase 4: Verify
  1. All tests pass
  2. No behavior changes (verify with integration tests)
  3. Performance not degraded
  4. Code coverage maintained
  
  Report progress after each phase. Stop immediately if tests fail.
```

#### `/add-feature` - Feature Implementation

```yaml
# .claude/skills/add-feature.yaml
name: add-feature
description: Implement a new feature following best practices
argumentHint: "<feature description>"
whenToUse: "Adding new functionality to the codebase"
allowedTools:
  - Bash
  - FileRead
  - FileEdit
  - FileWrite
  - Glob
  - Grep

prompt: |
  Implement the following feature: $ARGUMENTS
  
  ## Implementation Checklist
  
  ### Design
  - [ ] Understand requirements fully
  - [ ] Review similar existing features
  - [ ] Design API/interfaces first
  - [ ] Consider edge cases
  
  ### Implementation
  - [ ] Follow existing patterns in codebase
  - [ ] Write code incrementally
  - [ ] Add inline comments for complex logic
  - [ ] Keep functions small and focused
  
  ### Testing
  - [ ] Unit tests for new functions
  - [ ] Integration tests for feature flow
  - [ ] Edge case tests
  - [ ] Error condition tests
  
  ### Documentation
  - [ ] JSDoc/docstrings for public APIs
  - [ ] Update relevant README sections
  - [ ] Add usage examples
  - [ ] Document configuration options
  
  ### Verification
  - [ ] All tests pass
  - [ ] Linting passes
  - [ ] Type checking passes
  - [ ] Manual testing completed
  
  Ask clarifying questions before starting if requirements are unclear.
```

### 3. Debugging Skills

#### `/debug-issue` - Systematic Debugging

```yaml
# .claude/skills/debug-issue.yaml
name: debug-issue
description: Systematic debugging methodology
argumentHint: "<issue description or error message>"
whenToUse: "Investigating and fixing bugs"
allowedTools:
  - Bash
  - FileRead
  - FileEdit
  - Grep
  - Glob

prompt: |
  Debug the following issue: $ARGUMENTS
  
  ## Debugging Methodology
  
  ### 1. Reproduce
  - Document exact steps to reproduce
  - Identify consistent vs intermittent
  - Note environment details
  
  ### 2. Isolate
  - Narrow down affected code paths
  - Create minimal reproduction
  - Identify when issue started (git bisect)
  
  ### 3. Hypothesize
  - Generate possible causes
  - Rank by likelihood
  - Design tests for each hypothesis
  
  ### 4. Investigate
  - Add logging strategically
  - Use debugger where applicable
  - Check related systems/components
  
  ### 5. Fix
  - Implement minimal fix
  - Add regression test
  - Verify fix resolves issue
  
  ### 6. Prevent
  - Root cause analysis
  - Could this be caught earlier?
  - Update monitoring/alerting if needed
  
  Document findings at each step. Share hypotheses before testing.
```

#### `/performance-audit` - Performance Analysis

```yaml
# .claude/skills/performance-audit.yaml
name: performance-audit
description: Performance profiling and optimization
argumentHint: "[component or operation to analyze]"
whenToUse: "Identifying and fixing performance issues"
allowedTools:
  - Bash
  - FileRead
  - Grep
  - Glob

prompt: |
  Analyze performance of: $ARGUMENTS
  
  ## Performance Audit Steps
  
  ### Measurement
  1. Establish baseline metrics
  2. Identify slow operations
  3. Profile CPU/memory usage
  4. Check I/O patterns
  
  ### Analysis
  1. Find bottlenecks (CPU, memory, I/O, network)
  2. Identify N+1 queries or loops
  3. Check for unnecessary work
  4. Review algorithm complexity
  
  ### Optimization Opportunities
  1. Caching opportunities
  2. Batch operations
  3. Lazy loading
  4. Parallel processing
  5. Data structure improvements
  6. Query optimization
  
  ### Verification
  1. Measure improvement
  2. Check no regressions
  3. Validate under load
  4. Monitor in production
  
  Provide before/after benchmarks for any optimizations.
```

### 4. Documentation Skills

#### `/write-docs` - Documentation Generation

```yaml
# .claude/skills/write-docs.yaml
name: write-docs
description: Generate comprehensive documentation
argumentHint: "<what to document>"
whenToUse: "Creating or updating documentation"
allowedTools:
  - FileRead
  - FileWrite
  - Grep
  - Glob

prompt: |
  Write documentation for: $ARGUMENTS
  
  ## Documentation Standards
  
  ### Structure
  - Clear title and overview
  - Table of contents (for longer docs)
  - Prerequisites section
  - Step-by-step instructions
  - Examples with expected output
  - Troubleshooting section
  - Related resources
  
  ### Writing Style
  - Active voice
  - Second person ("you")
  - Concise sentences
  - Consistent terminology
  - Accessible language
  
  ### Code Examples
  - Complete, runnable examples
  - Comments explaining key parts
  - Expected output shown
  - Common variations included
  
  ### Maintenance
  - Last updated date
  - Version applicability
  - Owner/maintainer noted
  - Review schedule defined
  
  Output as Markdown. Include suggestions for diagrams if helpful.
```

### 5. Testing Skills

#### `/write-tests` - Test Generation

```yaml
# .claude/skills/write-tests.yaml
name: write-tests
description: Generate comprehensive test suites
argumentHint: "<code to test>"
whenToUse: "Creating tests for new or existing code"
allowedTools:
  - FileRead
  - FileWrite
  - Bash
  - Grep
  - Glob

prompt: |
  Write tests for: $ARGUMENTS
  
  ## Test Coverage Goals
  
  ### Unit Tests
  - [ ] Happy path scenarios
  - [ ] Edge cases (empty, null, boundary values)
  - [ ] Error conditions
  - [ ] All public methods/functions
  
  ### Integration Tests
  - [ ] Component interactions
  - [ ] Database operations
  - [ ] API endpoints
  - [ ] External service calls (mocked)
  
  ### Property-Based Tests
  - [ ] Invariants that should always hold
  - [ ] Input/output relationships
  - [ ] State transitions
  
  ### Test Quality
  - [ ] Tests are independent
  - [ ] Fast execution
  - [ ] Clear failure messages
  - [ ] No flaky tests
  - [ ] Good test names (describe behavior)
  
  Follow existing test patterns in the codebase. Aim for >80% coverage.
```

## Creating Custom Skills

### Skill Structure

```yaml
# .claude/skills/my-custom-skill.yaml
name: my-skill-name
description: Clear one-line description
aliases: ["alias1", "alias2"]  # Optional
argumentHint: "<arg1> [optional-arg2]"  # Shows in UI
whenToUse: "When you need to..."  # Guidance for model
allowedTools:
  - Bash
  - FileRead
  - FileEdit
model: claude-sonnet-4-20250514  # Optional, defaults to session model
disableModelInvocation: false  # Set true for pure prompt skills
context: inline  # or 'fork' for isolated context
hooks:  # Optional hooks
  pre_sampling:
    - type: exec
      command: echo "Preparing..."

prompt: |
  Your skill prompt goes here.
  
  Use $ARGUMENTS to reference user input.
  Use $ARGUMENTS[0] or $0 for first argument.
  Use $1, $2 for additional arguments.
  
  Structure your prompt clearly with sections.
  Include examples of expected behavior.
  Define success criteria.
```

### Skill with Embedded Files

```yaml
# .claude/skills/skill-with-files.yaml
name: template-generator
description: Generate code from templates
files:
  templates/component.hbs: |
    import React from 'react';
    
    interface {{name}}Props {
      {{#each props}}
      {{this.name}}: {{this.type}};
      {{/each}}
    }
    
    export const {{name}}: React.FC<{{name}}Props> = (props) => {
      return (
        <div className="{{name}}">
          {{content}}
        </div>
      );
    };

prompt: |
  Base directory for this skill: $SKILL_ROOT
  
  Generate a new component using the template in templates/component.hbs.
  
  User request: $ARGUMENTS
  
  1. Read the template file
  2. Parse user requirements
  3. Generate component code
  4. Write to appropriate location
  5. Create accompanying test file
```

## Best Practices

### Writing Effective Skills

1. **Clear Purpose**: One skill, one purpose
2. **Descriptive Name**: Self-explanatory naming
3. **Argument Hints**: Guide users on usage
4. **Tool Restrictions**: Only allow necessary tools
5. **Structured Prompts**: Use sections and checklists
6. **Examples**: Include example invocations
7. **Error Handling**: Define behavior for edge cases
8. **Testing**: Test skills like code

### Organizing Skills

```
.claude/skills/
├── review/
│   ├── pr.yaml
│   ├── security.yaml
│   └── performance.yaml
├── dev/
│   ├── refactor.yaml
│   ├── add-feature.yaml
│   └── debug.yaml
├── test/
│   ├── unit.yaml
│   ├── integration.yaml
│   └── e2e.yaml
└── docs/
    ├── api.yaml
    ├── readme.yaml
    └── changelog.yaml
```

### Versioning Skills

```yaml
# Include version in skill metadata
name: review-pr
version: 1.2.0
changelog:
  - version: 1.2.0
    changes: ["Added security checklist"]
  - version: 1.1.0
    changes: ["Added performance checks"]
```

## Contributing

To contribute new skill templates:

1. Create skill YAML in appropriate category
2. Test thoroughly with real use cases
3. Document usage with examples
4. Submit for review

## License

MIT
