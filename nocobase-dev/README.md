# nocobase-dev (Gemini CLI extension)

NocoBase v2 development plugin. Build, manage, and operate NocoBase through the `nb` CLI 2.2 (primary) or REST API (fallback). Bundles 20 official upstream skills from nocobase/skills (auto-synced weekly via GitHub Action; UI authoring enters through nocobase-portal-manage), 5 custom skills (overview, auth, cli-recipes, api-reference, examples), and an OpenAPI 3.0.3 snapshot of NocoBase v2.1.0-beta.29 (272 endpoints; refresh from a 2.2.x stand pending). Targets the stable @nocobase/cli channel (Node.js 22+); NocoBase 2.1.0+ for agent connection. No MCP server shipped; NocoBase has its own at /api/mcp. Env vars match upstream naming: NB_URL + NB_USER + NB_PASSWORD for sign-in flow, or NB_URL + NB_TOKEN for the long-lived API Key path.

## Status

Consumer access to the Gemini CLI closed on 2026-06-18; this extension is maintained for **enterprise Gemini Code Assist**, which still runs the Gemini CLI extension format (the consumer-facing successor is Antigravity CLI).

## Install

```bash
gemini extensions install https://github.com/Agents-Store/gemini-extensions
```

The [geminicli.com](https://geminicli.com) gallery — and the `install <url>` form above — only resolve a repository that carries `gemini-extension.json` at its **root**. This extension ships from the `agents-store-gemini-extensions` monorepo, where every plugin lives in its own subdirectory, so it will not appear in the gallery and the command above will not resolve directly. Until that repository is split one-plugin-per-repo, install locally instead:

```bash
git clone https://github.com/Agents-Store/gemini-extensions
gemini extensions link gemini-extensions/nocobase-dev
```

## Source

Canonical: https://github.com/agents-store/claude-public-plugins/tree/main/plugins/nocobase-dev
