# ahok.io Memory Agent Skill

Long-term memory for AI agents with [ahok.io](https://ahok.io). The skill lets an agent store facts, preferences and project context as memories, then recall them in later conversations through the ahok.io REST API or the hosted MCP endpoint `https://memtool.ahok.io/mcp`.

This repository holds the `ahok-memory` agent skill in [`skills/ahok-memory/`](skills/ahok-memory/SKILL.md). It is maintained by Epsimo AI. It was previously published as the `ahok-skill` repository; the skill name `ahok-memory` is unchanged.

## Install the agent skill

```bash
npx skills add epsimoai/ahok-memory
```

The `skills` CLI needs Node.js 22.20 or later. The REST examples in the skill use Python 3 with the `requests` package, or Node.js `fetch`.

Supported agent tools:

| Agent tool | Command | Project path | Global path (with `-g`) |
| --- | --- | --- | --- |
| Claude Code | `npx skills add epsimoai/ahok-memory -a claude-code` | `.claude/skills/ahok-memory/` | `~/.claude/skills/ahok-memory/` |
| OpenAI Codex CLI | `npx skills add epsimoai/ahok-memory -a codex` | `.agents/skills/ahok-memory/` | `~/.agents/skills/ahok-memory/` |
| Cursor | `npx skills add epsimoai/ahok-memory -a cursor` | `.agents/skills/ahok-memory/` | `~/.cursor/skills/ahok-memory/` |
| Kiro (IDE and CLI) | `npx skills add epsimoai/ahok-memory -a kiro-cli` | `.kiro/skills/ahok-memory/` | `~/.kiro/skills/ahok-memory/` |
| Other tools | `npx skills add epsimoai/ahok-memory`, then pick the tool | per tool | per tool |

In the Kiro IDE you can also import the skill from GitHub with the URL `https://github.com/epsimoai/ahok-memory/tree/main/skills/ahok-memory`. See the [skills CLI agent list](https://github.com/vercel-labs/skills/blob/main/docs/agents.md) for other tools.

### Connect the hosted MCP endpoint (optional)

Add this block to your MCP client configuration and replace the placeholder with your ahok.io API key:

```json
{
  "mcpServers": {
    "ahok-memory": {
      "url": "https://memtool.ahok.io/mcp",
      "headers": {
        "x-api-key": "<YOUR_AHOK_API_KEY>"
      }
    }
  }
}
```

### Credentials

You need an ahok.io account.

| Variable | Status | Where to get it |
| --- | --- | --- |
| `AHOK_API_KEY` | Required. Used by the REST API examples and sent as the `x-api-key` header, also to `https://memtool.ahok.io/mcp` | Create an API key in your ahok.io account and set it as `AHOK_API_KEY`. |

Keep keys out of chats, prompts and commits. Memories you store are sent to and kept on the ahok.io hosted service, so do not store passwords or other secrets as memories.

### Sample prompt

> Remember that I prefer TypeScript and that my current project is called "Atlas". Then recall what you know about my preferences.

[ahok.io homepage](https://ahok.io)

## Repository structure

- `skills/ahok-memory/SKILL.md`: instructions for AI agents.
- `skills/ahok-memory/references/api_reference.md`: REST API reference.
- `skills/ahok-memory/references/examples.md`: Python, TypeScript and MCP examples.

## License

Apache License 2.0. See [LICENSE](LICENSE) and [NOTICE](NOTICE). This skill includes material derived from OpenMemory by CaviraOSS, licensed under the Apache License 2.0.
