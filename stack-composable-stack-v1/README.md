# stack-composable-stack-v1 (Gemini CLI Extension)

Composable Stack v1 dev plugin. Integrates PostgreSQL (direct MCP + PostgREST API), NocoDB, n8n, Trigger.dev, and NocoBase (prod + dev sandbox via nc-mcp) for building data-driven applications with low-code interfaces.

## Install

```bash
gemini extensions install agents-store/gemini-ext-stack-composable-stack-v1
```

## Required environment variables

Set in `~/.gemini/settings.json` or shell environment:

- `N8N_API_KEY`
- `N8N_API_URL`
- `N8N_MCP_TOKEN`
- `N8N_NATIVE_MCP_URL`
- `NOCOBASE_DEV_API_KEY`
- `NOCOBASE_DEV_URL`
- `NOCODB_MCP_URL`
- `NOCODB_TOKEN`
- `POSTGRESQL_MCP_TOKEN`
- `POSTGRESQL_MCP_URL`
- `TRIGGER_API_URL`
- `TRIGGER_SECRET_KEY`

## Source

Canonical: https://github.com/agents-store/claude-public-plugins/tree/main/plugins/stack-composable-stack-v1
