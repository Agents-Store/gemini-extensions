# taiga-ops (Gemini CLI extension)

Taiga project-management ops plugin. Drive the full Taiga REST API by curl — projects, memberships, roles, milestones (sprints), epics, user stories, tasks, issues (with statuses, types, priorities, severities, points, custom attributes), wiki, history, attachments, comments, webhooks, notify policies, search, resolver, stats, and import/export. Authenticates with TAIGA_ADMIN_USERNAME + TAIGA_ADMIN_PASSWORD to obtain TAIGA_AUTH_TOKEN against TAIGA_API_URL.

## Status

Consumer access to the Gemini CLI closed on 2026-06-18; this extension is maintained for **enterprise Gemini Code Assist**, which still runs the Gemini CLI extension format (the consumer-facing successor is Antigravity CLI).

## Install

```bash
gemini extensions install https://github.com/Agents-Store/gemini-extensions
```

The [geminicli.com](https://geminicli.com) gallery — and the `install <url>` form above — only resolve a repository that carries `gemini-extension.json` at its **root**. This extension ships from the `agents-store-gemini-extensions` monorepo, where every plugin lives in its own subdirectory, so it will not appear in the gallery and the command above will not resolve directly. Until that repository is split one-plugin-per-repo, install locally instead:

```bash
git clone https://github.com/Agents-Store/gemini-extensions
gemini extensions link gemini-extensions/taiga-ops
```

## Source

Canonical: https://github.com/agents-store/claude-public-plugins/tree/main/plugins/taiga-ops
