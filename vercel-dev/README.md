# vercel-dev (Gemini CLI extension)

Vercel ecosystem plugin. Deployment, AI SDK, Edge Functions, storage, routing, performance optimization. Includes CLI deploy troubleshooting for non-Git projects, Hobby plan fixes, standalone output handling. Based on official vercel-plugin v0.25.0 by Vercel Labs.

## Status

Consumer access to the Gemini CLI closed on 2026-06-18; this extension is maintained for **enterprise Gemini Code Assist**, which still runs the Gemini CLI extension format (the consumer-facing successor is Antigravity CLI).

## Install

```bash
gemini extensions install https://github.com/Agents-Store/gemini-extensions
```

The [geminicli.com](https://geminicli.com) gallery — and the `install <url>` form above — only resolve a repository that carries `gemini-extension.json` at its **root**. This extension ships from the `agents-store-gemini-extensions` monorepo, where every plugin lives in its own subdirectory, so it will not appear in the gallery and the command above will not resolve directly. Until that repository is split one-plugin-per-repo, install locally instead:

```bash
git clone https://github.com/Agents-Store/gemini-extensions
gemini extensions link gemini-extensions/vercel-dev
```

## Source

Canonical: https://github.com/agents-store/claude-public-plugins/tree/main/plugins/vercel-dev
