# ClaudeCode Improvement Starter Kit

This folder provides ready-to-use templates for:
- **Skills** (research direction, parallel delivery, token-efficient responses)
- **MCP configurations** (research + knowledge server patterns)
- **Plugins** (commands, agents, hooks, output styles)
- **Prompt templates** (quality + token efficiency)

Each artifact includes both:
- **Short description**: one-line summary for quick scanning.
- **Long description**: implementation intent, scope, and expected behavior.

## Recommended usage

1. Copy selected files into your real runtime paths:
   - Skills: `~/.claude/skills/` or `.claude/skills/`
   - Plugin: `~/.claude/plugins/` or marketplace package
   - Output styles: `.claude/output-styles/`
   - MCP config: `.mcp.json` or user/project settings
2. Start with conservative tool permissions and narrow scope.
3. Iterate via benchmark prompts and compare answer quality + token count.
