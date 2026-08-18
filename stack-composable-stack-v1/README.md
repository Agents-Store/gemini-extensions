# stack-composable-stack-v1 (Gemini CLI extension)

Composable Stack v1 dev plugin. Integrates PostgreSQL (direct MCP + PostgREST API), NocoDB, n8n, Trigger.dev, and NocoBase (prod + dev sandbox via nc-mcp) for building data-driven applications with low-code interfaces.

## Status

Consumer access to the Gemini CLI closed on 2026-06-18; this extension is maintained for **enterprise Gemini Code Assist**, which still runs the Gemini CLI extension format (the consumer-facing successor is Antigravity CLI).

## Install

```bash
gemini extensions install https://github.com/Agents-Store/gemini-extensions
```

The [geminicli.com](https://geminicli.com) gallery — and the `install <url>` form above — only resolve a repository that carries `gemini-extension.json` at its **root**. This extension ships from the `agents-store-gemini-extensions` monorepo, where every plugin lives in its own subdirectory, so it will not appear in the gallery and the command above will not resolve directly. Until that repository is split one-plugin-per-repo, install locally instead:

```bash
git clone https://github.com/Agents-Store/gemini-extensions
gemini extensions link gemini-extensions/stack-composable-stack-v1
```

## Required environment variables

Declared in `gemini-extension.json`'s `settings[]` and prompted for on install/link:

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
