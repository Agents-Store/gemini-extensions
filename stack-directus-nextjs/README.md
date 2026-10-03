# stack-directus-nextjs (Gemini CLI extension)

Directus + Next.js architecture plugin. How Directus (content, files, access) and a Next.js App Router frontend fit together: who holds the token, how the cache is invalidated, how assets and types cross the boundary, who owns the session, and a production checklist. Tool knowledge comes from its dependencies.

## Status

Consumer access to the Gemini CLI closed on 2026-06-18; this extension is maintained for **enterprise Gemini Code Assist**, which still runs the Gemini CLI extension format (the consumer-facing successor is Antigravity CLI).

## Install

```bash
gemini extensions install https://github.com/Agents-Store/gemini-extensions
```

The [geminicli.com](https://geminicli.com) gallery — and the `install <url>` form above — only resolve a repository that carries `gemini-extension.json` at its **root**. This extension ships from the `agents-store-gemini-extensions` monorepo, where every plugin lives in its own subdirectory, so it will not appear in the gallery and the command above will not resolve directly. Until that repository is split one-plugin-per-repo, install locally instead:

```bash
git clone https://github.com/Agents-Store/gemini-extensions
gemini extensions link gemini-extensions/stack-directus-nextjs
```

## Required environment variables

Declared in `gemini-extension.json`'s `settings[]` and prompted for on install/link:

- `DIRECTUS_ADMIN_TOKEN`
- `NEXT_PUBLIC_DIRECTUS_URL`

## Source

Canonical: https://github.com/agents-store/claude-public-plugins/tree/main/plugins/stack-directus-nextjs
