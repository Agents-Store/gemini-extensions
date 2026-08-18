# n8n (Gemini CLI extension)

n8n workflow automation plugin. Manage workflows, execute automations, configure nodes, handle credentials, monitor executions, expression syntax, node configuration patterns, and code node best practices via MCP tools.

## Status

Consumer access to the Gemini CLI closed on 2026-06-18; this extension is maintained for **enterprise Gemini Code Assist**, which still runs the Gemini CLI extension format (the consumer-facing successor is Antigravity CLI).

## Install

```bash
gemini extensions install https://github.com/Agents-Store/gemini-extensions
```

The [geminicli.com](https://geminicli.com) gallery — and the `install <url>` form above — only resolve a repository that carries `gemini-extension.json` at its **root**. This extension ships from the `agents-store-gemini-extensions` monorepo, where every plugin lives in its own subdirectory, so it will not appear in the gallery and the command above will not resolve directly. Until that repository is split one-plugin-per-repo, install locally instead:

```bash
git clone https://github.com/Agents-Store/gemini-extensions
gemini extensions link gemini-extensions/n8n
```

## Required environment variables

Declared in `gemini-extension.json`'s `settings[]` and prompted for on install/link:

- `N8N_MCP_TOKEN`
- `N8N_MCP_URL`

## Source

Canonical: https://github.com/agents-store/claude-public-plugins/tree/main/plugins/n8n
