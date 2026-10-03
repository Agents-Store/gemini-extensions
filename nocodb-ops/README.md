# nocodb-ops (Gemini CLI extension)

NocoDB ops plugin for Agents Store. Record management, filtering (structured filters, exactDate date filters), sorting, reports, search, webhooks (events, payload, conditions), and data import/export for business users via the NocoDB MCP server (writes in batches of up to 100 records; extra tools on Cloud/licensed through listTools/callTool) and curl on the v3 API.

## Status

Consumer access to the Gemini CLI closed on 2026-06-18; this extension is maintained for **enterprise Gemini Code Assist**, which still runs the Gemini CLI extension format (the consumer-facing successor is Antigravity CLI).

## Install

```bash
gemini extensions install https://github.com/Agents-Store/gemini-extensions
```

The [geminicli.com](https://geminicli.com) gallery — and the `install <url>` form above — only resolve a repository that carries `gemini-extension.json` at its **root**. This extension ships from the `agents-store-gemini-extensions` monorepo, where every plugin lives in its own subdirectory, so it will not appear in the gallery and the command above will not resolve directly. Until that repository is split one-plugin-per-repo, install locally instead:

```bash
git clone https://github.com/Agents-Store/gemini-extensions
gemini extensions link gemini-extensions/nocodb-ops
```

## Required environment variables

Declared in `gemini-extension.json`'s `settings[]` and prompted for on install/link:

- `NOCODB_MCP_TOKEN`
- `NOCODB_MCP_URL`

## Source

Canonical: https://github.com/agents-store/claude-public-plugins/tree/main/plugins/nocodb-ops
