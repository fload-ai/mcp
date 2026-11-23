# Fload MCP

Fload's skill for AI assistants, and how to connect an assistant to Fload's MCP server.

## The skill

Install it with the skills CLI:

```
npx skills add fload-ai/mcp --skill fload
```

The skill is `skills/fload/SKILL.md`: the same file that each release of [`@fload-ai/mcp` on npm](https://www.npmjs.com/package/@fload-ai/mcp) ships as `skill/SKILL.md`, copied here unchanged. You can also copy that file into your assistant's skills folder yourself.

## Connect to Fload's MCP server

**Claude Code:** `claude mcp add --transport http --scope user fload https://api.fload.com/mcp`

**Claude (desktop and claude.ai):** Settings → Connectors → Add custom connector, name it Fload, URL `https://api.fload.com/mcp`.
