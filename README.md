# Claude Skills

A collection of open-source skills for Claude — content extraction, vault management, financial analysis, security auditing, and AI development.

Compatible with Claude.ai, Claude Code, and the [agentskills.io](https://agentskills.io) open standard.

## Skills

| Skill | Description |
|---|---|
| [post-extractor](post-extractor/) | Extract URLs, articles, and threads into structured Obsidian-ready notes with skepticism filtering |
| [chat-summary](chat-summary/) | Maintain rolling session summaries that restore full context across chats |
| [vault-triage](vault-triage/) | Survey an Obsidian inbox and produce structured triage reports |
| [skill-security-auditor](skill-security-auditor/) | Pre-flight security audit for external skills, scripts, and repos |
| [analyzing-financial-statements](analyzing-financial-statements/) | Calculate key financial ratios from statement data |
| [creating-financial-models](creating-financial-models/) | DCF analysis, sensitivity testing, Monte Carlo simulations |
| [skill-creator](skill-creator/) | Create, test, and iterate on new Claude skills |
| [claude-api](claude-api/) | Build apps with the Claude API and Anthropic SDKs |
| [mcp-builder](mcp-builder/) | Build MCP servers for LLM-service integration |

## Installation

### Claude Code (via npx skills CLI)

```bash
npx skills add spweps/claude-skills --skill post-extractor
```

### Claude.ai

1. Download the skill folder
2. Go to Settings → Skills → Upload
3. Upload the folder as a zip

### Manual

Copy the skill folder into your project's `.claude/skills/` directory (project scope) or `~/.claude/skills/` (global scope).

## Structure

Each skill follows the [agentskills.io](https://agentskills.io) open standard:

```
skill-name/
├── SKILL.md          # Required — instructions and trigger logic
├── references/       # Optional — loaded on demand
└── scripts/          # Optional — deterministic validation
```

## Attribution

- `skill-creator`, `claude-api`, `mcp-builder` are adapted from [Anthropic's official skills](https://github.com/anthropics/skills) (MIT).
- `analyzing-financial-statements`, `creating-financial-models` are adapted from [Anthropic's Claude Cookbooks](https://github.com/anthropics/claude-cookbooks) (MIT).
- All other skills are original work.

## License

MIT
