# openclaw-configurator (Gemini CLI extension)

DEPRECATED — superseded by openclaw-ops, which operates instances from live Docker state instead of hard-coded paths. Kept for its workspace-authoring skills (AGENTS.md, SOUL.md, personas, standing orders); use openclaw-ops for anything operational. OpenClaw instance configurator and operations plugin. Scan, analyze, and optimize all workspace files (AGENTS.md, SOUL.md, USER.md, IDENTITY.md, TOOLS.md, HEARTBEAT.md, MEMORY.md, BOOT.md, BOOTSTRAP.md) plus openclaw.json. Update instances from official GitHub releases and reconcile config against new releases (recommend new features, migrate legacy settings, run openclaw doctor). Set up model-provider authentication with an OAuth/CLI-backend-first, cost-saving bias (Claude CLI, Codex OAuth). Migrate .env secrets into self-hosted Infisical. Guided interviews, session log analysis, standing orders design, security audit, config validation, permission fix hooks, centralized doc research, and 6 industry-specific workspace templates.

## Status

Consumer access to the Gemini CLI closed on 2026-06-18; this extension is maintained for **enterprise Gemini Code Assist**, which still runs the Gemini CLI extension format (the consumer-facing successor is Antigravity CLI).

## Install

```bash
gemini extensions install https://github.com/Agents-Store/gemini-extensions
```

The [geminicli.com](https://geminicli.com) gallery — and the `install <url>` form above — only resolve a repository that carries `gemini-extension.json` at its **root**. This extension ships from the `agents-store-gemini-extensions` monorepo, where every plugin lives in its own subdirectory, so it will not appear in the gallery and the command above will not resolve directly. Until that repository is split one-plugin-per-repo, install locally instead:

```bash
git clone https://github.com/Agents-Store/gemini-extensions
gemini extensions link gemini-extensions/openclaw-configurator
```

## Source

Canonical: https://github.com/agents-store/claude-public-plugins/tree/main/plugins/openclaw-configurator
