# mattermost-ops (Gemini CLI extension)

Mattermost collaboration ops plugin. Drive the full Mattermost REST API v4 by curl — users, teams, channels (public/private/DM/group), posts & threads, reactions, files, custom emoji, webhooks, slash commands, bots, OAuth apps, plus system administration: config, RBAC roles & schemes, LDAP/SAML groups, compliance, data retention, plugins, jobs, and analytics. Authenticates with MATTERMOST_ADMIN_USERNAME + MATTERMOST_ADMIN_PASSWORD to obtain a session token against MATTERMOST_API_URL.

## Status

Consumer access to the Gemini CLI closed on 2026-06-18; this extension is maintained for **enterprise Gemini Code Assist**, which still runs the Gemini CLI extension format (the consumer-facing successor is Antigravity CLI).

## Install

```bash
gemini extensions install https://github.com/Agents-Store/gemini-extensions
```

The [geminicli.com](https://geminicli.com) gallery — and the `install <url>` form above — only resolve a repository that carries `gemini-extension.json` at its **root**. This extension ships from the `agents-store-gemini-extensions` monorepo, where every plugin lives in its own subdirectory, so it will not appear in the gallery and the command above will not resolve directly. Until that repository is split one-plugin-per-repo, install locally instead:

```bash
git clone https://github.com/Agents-Store/gemini-extensions
gemini extensions link gemini-extensions/mattermost-ops
```

## Source

Canonical: https://github.com/agents-store/claude-public-plugins/tree/main/plugins/mattermost-ops
