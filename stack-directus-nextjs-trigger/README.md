# stack-directus-nextjs-trigger (Gemini CLI extension)

Directus + Next.js + Trigger.dev architecture plugin. How Directus (content, files, access), a Next.js App Router frontend and self-hosted Trigger.dev (durable and scheduled work) fit together: who holds which token, how the cache is invalidated, how a Directus change reaches a task and the result reaches the page, who owns the session, and a production checklist. Tool knowledge comes from its dependencies.

## Status

Consumer access to the Gemini CLI closed on 2026-06-18; this extension is maintained for **enterprise Gemini Code Assist**, which still runs the Gemini CLI extension format (the consumer-facing successor is Antigravity CLI).

## Install

```bash
gemini extensions install https://github.com/Agents-Store/gemini-extensions
```

The [geminicli.com](https://geminicli.com) gallery — and the `install <url>` form above — only resolve a repository that carries `gemini-extension.json` at its **root**. This extension ships from the `agents-store-gemini-extensions` monorepo, where every plugin lives in its own subdirectory, so it will not appear in the gallery and the command above will not resolve directly. Until that repository is split one-plugin-per-repo, install locally instead:

```bash
git clone https://github.com/Agents-Store/gemini-extensions
gemini extensions link gemini-extensions/stack-directus-nextjs-trigger
```

## Required environment variables

Declared in `gemini-extension.json`'s `settings[]` and prompted for on install/link:

- `DIRECTUS_ADMIN_TOKEN`
- `NEXT_PUBLIC_DIRECTUS_URL`
- `TRIGGER_ACCESS_TOKEN`
- `TRIGGER_API_URL`
- `TRIGGER_PROJECT_REF`

## Source

Canonical: https://github.com/agents-store/claude-public-plugins/tree/main/plugins/stack-directus-nextjs-trigger
