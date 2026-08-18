# atlassian-ops (Gemini CLI extension)

Atlassian Jira + Confluence Cloud ops plugin. Drive the full Jira Cloud REST API v3 and Confluence Cloud REST API v2 by curl — Jira: issues (create/edit/transition/assign, ADF bodies), JQL search, comments & worklogs, attachments & links, projects/versions/components, fields & screens, workflows/types/statuses, users & groups (accountId), permission/notification/security schemes, dashboards & filters, plans & teams; Confluence: pages & blog posts (versioned, storage/ADF bodies), spaces & permissions, comments & attachments, labels & content properties. Authenticates with Atlassian Cloud Basic auth (ATLASSIAN_EMAIL + ATLASSIAN_API_TOKEN) against ATLASSIAN_SITE_URL.

## Status

Consumer access to the Gemini CLI closed on 2026-06-18; this extension is maintained for **enterprise Gemini Code Assist**, which still runs the Gemini CLI extension format (the consumer-facing successor is Antigravity CLI).

## Install

```bash
gemini extensions install https://github.com/Agents-Store/gemini-extensions
```

The [geminicli.com](https://geminicli.com) gallery — and the `install <url>` form above — only resolve a repository that carries `gemini-extension.json` at its **root**. This extension ships from the `agents-store-gemini-extensions` monorepo, where every plugin lives in its own subdirectory, so it will not appear in the gallery and the command above will not resolve directly. Until that repository is split one-plugin-per-repo, install locally instead:

```bash
git clone https://github.com/Agents-Store/gemini-extensions
gemini extensions link gemini-extensions/atlassian-ops
```

## Source

Canonical: https://github.com/agents-store/claude-public-plugins/tree/main/plugins/atlassian-ops
