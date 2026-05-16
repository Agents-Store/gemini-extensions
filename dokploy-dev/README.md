# dokploy-dev (Gemini CLI Extension)

Dokploy self-hosted PaaS development plugin. Deploy applications, provision databases (Postgres, MySQL, MariaDB, MongoDB, Redis, LibSQL), manage domains, Docker Compose stacks, backups, and server operations via the official @dokploy/mcp server (500+ tools across 49 categories), 463 REST API endpoints, and CLI.

## Install

```bash
gemini extensions install agents-store/gemini-ext-dokploy-dev
```

## Required environment variables

Set in `~/.gemini/settings.json` or shell environment:

- `DOKPLOY_API_KEY`
- `DOKPLOY_URL`

## Source

Canonical: https://github.com/agents-store/claude-public-plugins/tree/main/plugins/dokploy-dev
