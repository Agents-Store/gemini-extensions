# nocobase-dev (Gemini CLI extension)

NocoBase v2 development plugin. Build, manage, and operate NocoBase through the `nb` CLI (primary) or REST API (fallback). Bundles 11 official upstream skills from nocobase/skills (auto-synced weekly via GitHub Action), 5 custom REST-API skills (overview, auth, cli-recipes, api-reference, examples), and the full OpenAPI 3.0.3 spec for NocoBase v2.1.0-beta.29 (272 endpoints across 19 tag groups). No MCP server. Env vars match upstream naming: NB_URL + NB_USER + NB_PASSWORD for sign-in flow, or NB_URL + NB_TOKEN for the long-lived API Key path.

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
