# payloadcms-dev (Gemini CLI extension)

PayloadCMS dev plugin for Agents Store. Covers collections, fields, globals, hooks, access control, authentication, queries, data management (trash/query presets/folders), localization, adapters, Lexical rich text, admin customization, jobs queue, plugin development, official plugins, Next.js integration, deployment, CLI, migrations, and end-to-end scenarios for TypeScript developers building with Payload v3.

## Status

Consumer access to the Gemini CLI closed on 2026-06-18; this extension is maintained for **enterprise Gemini Code Assist**, which still runs the Gemini CLI extension format (the consumer-facing successor is Antigravity CLI).

## Install

```bash
gemini extensions install https://github.com/Agents-Store/gemini-extensions
```

The [geminicli.com](https://geminicli.com) gallery — and the `install <url>` form above — only resolve a repository that carries `gemini-extension.json` at its **root**. This extension ships from the `agents-store-gemini-extensions` monorepo, where every plugin lives in its own subdirectory, so it will not appear in the gallery and the command above will not resolve directly. Until that repository is split one-plugin-per-repo, install locally instead:

```bash
git clone https://github.com/Agents-Store/gemini-extensions
gemini extensions link gemini-extensions/payloadcms-dev
```

## Source

Canonical: https://github.com/agents-store/claude-public-plugins/tree/main/plugins/payloadcms-dev
