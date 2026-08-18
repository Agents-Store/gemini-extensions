# chatwoot-dev (Gemini CLI extension)

Chatwoot dev plugin for Agents Store. Full REST API coverage (Application, Platform, and Public/Client APIs) with bundled OpenAPI specs, official chatwoot CLI recipes, webhook & agent-bot automation, and troubleshooting for developers building on Chatwoot. Authenticates with the api_access_token header via CHATWOOT_API_KEY against CHATWOOT_BASE_URL.

## Status

Consumer access to the Gemini CLI closed on 2026-06-18; this extension is maintained for **enterprise Gemini Code Assist**, which still runs the Gemini CLI extension format (the consumer-facing successor is Antigravity CLI).

## Install

```bash
gemini extensions install https://github.com/Agents-Store/gemini-extensions
```

The [geminicli.com](https://geminicli.com) gallery — and the `install <url>` form above — only resolve a repository that carries `gemini-extension.json` at its **root**. This extension ships from the `agents-store-gemini-extensions` monorepo, where every plugin lives in its own subdirectory, so it will not appear in the gallery and the command above will not resolve directly. Until that repository is split one-plugin-per-repo, install locally instead:

```bash
git clone https://github.com/Agents-Store/gemini-extensions
gemini extensions link gemini-extensions/chatwoot-dev
```

## Source

Canonical: https://github.com/agents-store/claude-public-plugins/tree/main/plugins/chatwoot-dev
