# dokploy-dev (Gemini CLI Extension)

Dokploy self-hosted PaaS development plugin (aligned with Dokploy v0.29.5). Deploy applications, provision 6 database types (Postgres, MySQL, MariaDB, MongoDB, Redis, LibSQL), manage domains and Docker Compose stacks, AND debug failed deployments end-to-end — reads runtime logs of every container (including each container in a Docker Compose stack) over the API/MCP with tail/since/search, plus AI-powered log analysis (ai-analyzeLogs), Docker container introspection, Traefik diagnosis, and a guided recovery chain. Complete MCP/REST coverage: all 526 v0.29.5 operations across 49 categories indexed with params. Uses the official @dokploy/mcp server plus debugging-focused slash commands including /compose-logs.

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
