# dokploy-dev (Gemini CLI Extension)

Dokploy self-hosted PaaS development plugin (aligned with Dokploy v0.29.x). Deploy applications, provision 6 database types (Postgres, MySQL, MariaDB, MongoDB, Redis, LibSQL), manage domains and Docker Compose stacks, AND debug failed deployments end-to-end with AI-powered log analysis (ai-analyzeLogs), Docker container introspection, Traefik diagnosis, and a guided recovery chain. Uses the official @dokploy/mcp server (500+ tools across 49 categories) plus 5 debugging-focused slash commands.

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
