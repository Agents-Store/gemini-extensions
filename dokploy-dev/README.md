# dokploy-dev (Gemini CLI extension)

Dokploy self-hosted PaaS development plugin (aligned with Dokploy v0.30.x). Deploy applications, provision 6 database types (Postgres, MySQL, MariaDB, MongoDB, Redis, LibSQL), manage domains and Docker Compose stacks, AND debug failed deployments end-to-end — reads runtime logs of every container (including each container in a Docker Compose stack) over the API/MCP with tail/since/search, plus AI-powered log analysis (ai-analyzeLogs), Docker container and host introspection (server health, events, disk usage, images, volumes), Traefik diagnosis, and a guided recovery chain. Complete MCP/REST coverage: all 604 operations across 57 categories indexed with params — covers per-service Docker network management, vault (external secrets) providers, DNS providers, forward-auth SSO domain protection, SCIM provisioning, build concurrency, and the auto-generated @dokploy/cli (604 commands incl. read-logs). Uses the official @dokploy/mcp server (secret fields are redacted by default since 0.30.0) plus debugging-focused slash commands including /compose-logs.

## Status

Consumer access to the Gemini CLI closed on 2026-06-18; this extension is maintained for **enterprise Gemini Code Assist**, which still runs the Gemini CLI extension format (the consumer-facing successor is Antigravity CLI).

## Install

```bash
gemini extensions install https://github.com/Agents-Store/gemini-extensions
```

The [geminicli.com](https://geminicli.com) gallery — and the `install <url>` form above — only resolve a repository that carries `gemini-extension.json` at its **root**. This extension ships from the `agents-store-gemini-extensions` monorepo, where every plugin lives in its own subdirectory, so it will not appear in the gallery and the command above will not resolve directly. Until that repository is split one-plugin-per-repo, install locally instead:

```bash
git clone https://github.com/Agents-Store/gemini-extensions
gemini extensions link gemini-extensions/dokploy-dev
```

## Required environment variables

Declared in `gemini-extension.json`'s `settings[]` and prompted for on install/link:

- `DOKPLOY_API_KEY`
- `DOKPLOY_URL`

## Source

Canonical: https://github.com/agents-store/claude-public-plugins/tree/main/plugins/dokploy-dev
