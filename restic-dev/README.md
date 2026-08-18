# restic-dev (Gemini CLI extension)

restic backup plugin for Agents Store. Set up encrypted daily backups on any Linux server to S3-compatible storage (Cloudflare R2): server recon + restic install, auto-discovery of all Docker volumes/mounts and databases, R2 repository init, a partial-failure-tolerant backup script with logical DB dumps and retention, timezone-aware systemd/cron scheduling, verification, monitoring/dead-man's-switch, and disaster recovery. File-based knowledge, no MCP, no stored credentials.

## Status

Consumer access to the Gemini CLI closed on 2026-06-18; this extension is maintained for **enterprise Gemini Code Assist**, which still runs the Gemini CLI extension format (the consumer-facing successor is Antigravity CLI).

## Install

```bash
gemini extensions install https://github.com/Agents-Store/gemini-extensions
```

The [geminicli.com](https://geminicli.com) gallery — and the `install <url>` form above — only resolve a repository that carries `gemini-extension.json` at its **root**. This extension ships from the `agents-store-gemini-extensions` monorepo, where every plugin lives in its own subdirectory, so it will not appear in the gallery and the command above will not resolve directly. Until that repository is split one-plugin-per-repo, install locally instead:

```bash
git clone https://github.com/Agents-Store/gemini-extensions
gemini extensions link gemini-extensions/restic-dev
```

## Source

Canonical: https://github.com/agents-store/claude-public-plugins/tree/main/plugins/restic-dev
