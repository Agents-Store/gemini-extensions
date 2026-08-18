# n8n-provision (Gemini CLI extension)

n8n instance provisioning plugin. Discover workflows from the official template library (9,166+ templates), GitHub repos, and community platforms, then analyze, import, and batch-deploy them to provision an n8n instance.

## Status

Consumer access to the Gemini CLI closed on 2026-06-18; this extension is maintained for **enterprise Gemini Code Assist**, which still runs the Gemini CLI extension format (the consumer-facing successor is Antigravity CLI).

## Install

```bash
gemini extensions install https://github.com/Agents-Store/gemini-extensions
```

The [geminicli.com](https://geminicli.com) gallery — and the `install <url>` form above — only resolve a repository that carries `gemini-extension.json` at its **root**. This extension ships from the `agents-store-gemini-extensions` monorepo, where every plugin lives in its own subdirectory, so it will not appear in the gallery and the command above will not resolve directly. Until that repository is split one-plugin-per-repo, install locally instead:

```bash
git clone https://github.com/Agents-Store/gemini-extensions
gemini extensions link gemini-extensions/n8n-provision
```

## Source

Canonical: https://github.com/agents-store/claude-public-plugins/tree/main/plugins/n8n-provision
