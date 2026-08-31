# openclaw-ops (Gemini CLI extension)

Operations plugin for a fleet of self-hosted OpenClaw gateway instances running as Docker Compose projects on one host. Discovers every instance from the live Docker state (never from hard-coded paths), classifies it ok/degraded/down/alien, and runs day-two maintenance: health and liveness reporting, provider-auth triage (expired, emptied and shadowed OAuth profiles, shared-credential token sink), config surgery with snapshot and executable rollback, memory/embedding repair and reindexing, shared skills and plugins consolidation, Infisical secret-delivery audit by key name only, security audit, version-drift and channel-aware upgrades, and reference-instance cloning. Mutations are dry-run by default behind an eight-block plan, need --yes, and need a typed confirmation when irreversible. Secrets are reported as fingerprints, presence and expiry — never as values. File-based knowledge: no MCP server, no required environment variables, no stored credentials; the single optional variable OPENCLAW_OPS_CONFIG is an escape hatch for the fleet-config path, and deployment specifics live in that operator-owned config outside the repository.

## Status

Consumer access to the Gemini CLI closed on 2026-06-18; this extension is maintained for **enterprise Gemini Code Assist**, which still runs the Gemini CLI extension format (the consumer-facing successor is Antigravity CLI).

## Install

```bash
gemini extensions install https://github.com/Agents-Store/gemini-extensions
```

The [geminicli.com](https://geminicli.com) gallery — and the `install <url>` form above — only resolve a repository that carries `gemini-extension.json` at its **root**. This extension ships from the `agents-store-gemini-extensions` monorepo, where every plugin lives in its own subdirectory, so it will not appear in the gallery and the command above will not resolve directly. Until that repository is split one-plugin-per-repo, install locally instead:

```bash
git clone https://github.com/Agents-Store/gemini-extensions
gemini extensions link gemini-extensions/openclaw-ops
```

## Source

Canonical: https://github.com/agents-store/claude-public-plugins/tree/main/plugins/openclaw-ops
