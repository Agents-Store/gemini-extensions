# restic-dev (Gemini CLI Extension)

restic backup plugin for Agents Store. Set up encrypted daily backups on any Linux server to S3-compatible storage (Cloudflare R2): server recon + restic install, auto-discovery of all Docker volumes/mounts and databases, R2 repository init, a partial-failure-tolerant backup script with logical DB dumps and retention, timezone-aware systemd/cron scheduling, verification, monitoring/dead-man's-switch, and disaster recovery. File-based knowledge, no MCP, no stored credentials.

## Install

```bash
gemini extensions install agents-store/gemini-ext-restic-dev
```

## Source

Canonical: https://github.com/agents-store/claude-public-plugins/tree/main/plugins/restic-dev
