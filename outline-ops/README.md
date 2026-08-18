# outline-ops (Gemini CLI extension)

Outline knowledge-base ops plugin. Drive the full Outline REST API by curl — documents (create, search, move, archive, trash, import/export, AI answers, memberships), collections (CRUD, user/group permissions, export), comments, stars, views, shares & access requests, users & groups, attachments & file operations, revisions, templates, events (audit log), OAuth clients, and data attributes. Authenticates with a Bearer OUTLINE_API_KEY against OUTLINE_API_URL.

## Status

Consumer access to the Gemini CLI closed on 2026-06-18; this extension is maintained for **enterprise Gemini Code Assist**, which still runs the Gemini CLI extension format (the consumer-facing successor is Antigravity CLI).

## Install

```bash
gemini extensions install https://github.com/Agents-Store/gemini-extensions
```

The [geminicli.com](https://geminicli.com) gallery — and the `install <url>` form above — only resolve a repository that carries `gemini-extension.json` at its **root**. This extension ships from the `agents-store-gemini-extensions` monorepo, where every plugin lives in its own subdirectory, so it will not appear in the gallery and the command above will not resolve directly. Until that repository is split one-plugin-per-repo, install locally instead:

```bash
git clone https://github.com/Agents-Store/gemini-extensions
gemini extensions link gemini-extensions/outline-ops
```

## Source

Canonical: https://github.com/agents-store/claude-public-plugins/tree/main/plugins/outline-ops
