# vaultwarden-dev (Gemini CLI extension)

Vaultwarden dev plugin for Agents Store. Script and integrate a self-hosted Vaultwarden (Bitwarden-compatible) server: what the client API can and cannot do under end-to-end encryption, the identity token flows, organization member management, the /admin panel API, the Bitwarden CLI (bw, bw serve), python-vaultwarden and Terraform, client/server version compatibility, troubleshooting, and a guard hook that asks before a command prints decrypted secrets into the chat. File-based knowledge, no MCP, no stored credentials.

## Status

Consumer access to the Gemini CLI closed on 2026-06-18; this extension is maintained for **enterprise Gemini Code Assist**, which still runs the Gemini CLI extension format (the consumer-facing successor is Antigravity CLI).

## Install

```bash
gemini extensions install https://github.com/Agents-Store/gemini-extensions
```

The [geminicli.com](https://geminicli.com) gallery — and the `install <url>` form above — only resolve a repository that carries `gemini-extension.json` at its **root**. This extension ships from the `agents-store-gemini-extensions` monorepo, where every plugin lives in its own subdirectory, so it will not appear in the gallery and the command above will not resolve directly. Until that repository is split one-plugin-per-repo, install locally instead:

```bash
git clone https://github.com/Agents-Store/gemini-extensions
gemini extensions link gemini-extensions/vaultwarden-dev
```

## Source

Canonical: https://github.com/agents-store/claude-public-plugins/tree/main/plugins/vaultwarden-dev
