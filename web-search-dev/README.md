# web-search-dev (Gemini CLI extension)

Web search and scraping developer toolkit. MCP tool patterns, REST API reference (Firecrawl v2), SDK/CLI usage for Firecrawl, Exa, Perplexity, Jina, Pexels, Unsplash, and Context7. Practical skills for web scraping, documentation search, and media discovery in dev workflows.

## Status

Consumer access to the Gemini CLI closed on 2026-06-18; this extension is maintained for **enterprise Gemini Code Assist**, which still runs the Gemini CLI extension format (the consumer-facing successor is Antigravity CLI).

## Install

```bash
gemini extensions install https://github.com/Agents-Store/gemini-extensions
```

The [geminicli.com](https://geminicli.com) gallery — and the `install <url>` form above — only resolve a repository that carries `gemini-extension.json` at its **root**. This extension ships from the `agents-store-gemini-extensions` monorepo, where every plugin lives in its own subdirectory, so it will not appear in the gallery and the command above will not resolve directly. Until that repository is split one-plugin-per-repo, install locally instead:

```bash
git clone https://github.com/Agents-Store/gemini-extensions
gemini extensions link gemini-extensions/web-search-dev
```

## Required environment variables

Declared in `gemini-extension.json`'s `settings[]` and prompted for on install/link:

- `CONTEXT7_API_KEY`
- `EXA_API_KEY`
- `FIRECRAWL_API_TOKEN`
- `JINA_API_KEY`
- `PERPLEXITY_API_KEY`

## Source

Canonical: https://github.com/agents-store/claude-public-plugins/tree/main/plugins/web-search-dev
