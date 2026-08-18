# teams-dev (Gemini CLI extension)

Microsoft Teams SDK dev plugin for Agents Store. TypeScript-first guidance for building Teams bots, message extensions, tabs, dialogs, and AI agents using @microsoft/teams.* packages — covers the App framework, activity routing, Adaptive Cards, AI/MCP/A2A, Microsoft Graph, SSO, sovereign clouds, the Teams CLI, devtools, sideloading, and end-to-end scenarios.

## Status

Consumer access to the Gemini CLI closed on 2026-06-18; this extension is maintained for **enterprise Gemini Code Assist**, which still runs the Gemini CLI extension format (the consumer-facing successor is Antigravity CLI).

## Install

```bash
gemini extensions install https://github.com/Agents-Store/gemini-extensions
```

The [geminicli.com](https://geminicli.com) gallery — and the `install <url>` form above — only resolve a repository that carries `gemini-extension.json` at its **root**. This extension ships from the `agents-store-gemini-extensions` monorepo, where every plugin lives in its own subdirectory, so it will not appear in the gallery and the command above will not resolve directly. Until that repository is split one-plugin-per-repo, install locally instead:

```bash
git clone https://github.com/Agents-Store/gemini-extensions
gemini extensions link gemini-extensions/teams-dev
```

## Source

Canonical: https://github.com/agents-store/claude-public-plugins/tree/main/plugins/teams-dev
