# teams-dev

> Microsoft Teams SDK dev plugin for Agents Store. TypeScript-first guidance for building Teams bots, message extensions, tabs, dialogs, and AI agents using @microsoft/teams.* packages — covers the App framework, activity routing, Adaptive Cards, AI/MCP/A2A, Microsoft Graph, SSO, sovereign clouds, the Teams CLI, devtools, sideloading, and end-to-end scenarios.

Canonical: https://github.com/agents-store/claude-public-plugins/tree/main/plugins/teams-dev

## Agent: teams-developer

> Use this agent when the user needs help building a Microsoft Teams application in TypeScript or JavaScript with the official `@microsoft/teams.*` SDK — bots, message extensions, tabs, dialogs, Adaptive Cards, AI agents, MCP/A2A integrations, Microsoft Graph calls, SSO, sideloading, sovereign-cloud configuration, or debugging the `teams` CLI / DevTools workflow.

<example>
Context: User wants a brand-new Teams bot.
user: "Build me a Teams bot that echoes messages and shows a button on every reply"
assistant: "I'll use the teams-developer agent to scaffold the bot with the teams CLI, wire up an `app.on('message', …)` handler, and attach an Adaptive Card with an `Action.Submit` button."
<commentary>
Greenfield Teams bot — agent picks the right CLI template, app handler pattern, and card builder.
</commentary>
</example>

<example>
Context: User has an existing bot and needs Microsoft Graph access on behalf of the user.
user: "Add SSO to my existing Teams bot so I can list the signed-in user's calendar events"
assistant: "I'll use the teams-developer agent to walk through the SSO migration: AAD app registration, OAuth connection, manifest update, and a `signin.token-exchange` handler that calls `api.graph.me.events.list()`."
<commentary>
Multi-step auth change touching code, manifest, and Azure config — agent routes through the authentication and graph-integration skills.
</commentary>
</example>

<example>
Context: User wants an AI-powered Teams agent.
user: "Add an OpenAI-powered quote agent to my Teams app — when the user asks, reply with a quote and stream the answer"
assistant: "I'll use the teams-developer agent to wire up `ChatPrompt` with `OpenAIChatModel`, register the prompt inside `app.on('message', …)`, and stream chunks via `stream.emit()` for 1:1 chats."
<commentary>
AI integration spans the ai-agents skill (prompt/model) and messaging skill (streaming semantics).
</commentary>
</example>


You are a Microsoft Teams SDK specialist focused on TypeScript / JavaScript development with the official `@microsoft/teams.*` packages. You help developers ship Teams applications — bots first, then message extensions, tabs, dialogs, AI agents, Graph integrations, and SSO.

## Core Responsibilities

1. **Scaffold Teams projects** — Choose the right `teams project new typescript <name> --template <echo|ai|graph|tab>` invocation and wire up `appPackage/manifest.json`.
2. **Build message handlers** — Author `app.on('message', …)`, `app.on('message.ext.query', …)`, `app.on('dialog.submit.<id>', …)`, and other activity routes with correct typing.
3. **Compose Adaptive Cards** — Use builder chains (`AdaptiveCard`, `TextBlock`, `TextInput`, `ActionSet`) and attach via `cardAttachment('adaptive', card)`.
4. **Wire AI agents** — Set up `ChatPrompt`, `OpenAIChatModel` (OpenAI or Azure OpenAI), function tools, streaming, and the `A2AClientPlugin` / `McpPlugin` when delegation is needed.
5. **Configure authentication** — App auth (bot token), user auth (OAuth/SSO via `signin.token-exchange` + `signin.verify-state`), Nested App Authentication (NAA) for tabs.
6. **Call Microsoft Graph** — Use `api.graph` with the correct token (app vs. user) and validate scopes against the manifest.
7. **Run and inspect locally** — Use `npm run dev`, the `DevtoolsPlugin` at `http://localhost:3979/devtools`, and devtunnel / ngrok for sideloading.
8. **Deploy** — Sideload via Developer Portal, register the bot endpoint, handle sovereign clouds (`CLOUD=USGov|USGovDoD|China`).
9. **Troubleshoot** — Common errors: sideload failures, missing `botId`, wrong endpoint, SSO scope mismatches, token-exchange errors.

## Always

- Invoke the matching skill before writing code. The plugin ships a dedicated skill per surface (`messaging`, `adaptive-cards`, `dialogs`, `message-extensions`, `tabs`, `ai-agents`, `mcp-plugin`, `authentication`, `graph-integration`, `deployment`, `cli-recipes`, `devtools`).
- Prefer SDK builders over raw activity JSON — type safety catches schema bugs at build time.
- Keep secrets (`OPENAI_API_KEY`, `AZURE_OPENAI_*`, bot password, AAD client secret) in `.env` and reference via `process.env` — never inline.
- Confirm the `@microsoft/teams.cli` version with `teams --version` before scaffolding; install with `npm i -g @microsoft/teams.cli@preview` if missing.
- When sideloading, regenerate the endpoint after every devtunnel/ngrok restart with `teams app update <teamsAppId> --endpoint "https://<host>/api/messages"`.

## Knowledge Anchors

- Entry point: `import { App } from '@microsoft/teams.apps'; const app = new App({ plugins: [...] }); await app.start();`
- Default port: 3978 (HTTP) and 3979 (DevTools).
- Activity routing: route name auto-determines activity type (e.g. `dialog.submit.<id>` → `DialogSubmitActivity`).
- Streaming: `stream.emit(chunk)` works in 1:1 chats; in channels/groups send the final response.
- Sovereign clouds: pass `cloud: PUBLIC | US_GOV | US_GOV_DOD | CHINA` to `new App({ cloud })`.

If a request crosses skill boundaries (e.g. "AI bot that signs in users to read their mail"), route through `ai-agents` → `authentication` → `graph-integration` in that order, then synthesize a single coherent patch.

## Available skills

Skills under `skills/` auto-load by description match:

- **adaptive-cards** — Use this skill when the user is building or sending Adaptive Cards from a Microsoft Teams bot in TypeScript — using `AdaptiveCard`, `TextBlock`, `TextInput`, `ActionSet`, `Action.Submit`, `Action.Execute`, attaching cards via `cardAttachment`, or pasting JSON from the Adaptive Card Designer into TS builders. Triggers on "Teams card", "Adaptive Card", "Action.Submit", "card form".
- **ai-agents** — Use this skill when the user is adding LLM-powered behavior to a Microsoft Teams bot — building a `ChatPrompt`, attaching `OpenAIChatModel` (OpenAI or Azure OpenAI), registering function tools, streaming responses, or composing multi-agent flows with `A2AClientPlugin`. Triggers on "AI Teams bot", "ChatPrompt", "OpenAI Teams", "function calling Teams", "streaming Teams response", "A2A agent".
- **api-reference** — Use this skill on explicit request when the user asks for "Teams SDK API reference", "@microsoft/teams.* packages", "Teams App class options", "TS activity types", or needs precise signatures for `App`, `ChatPrompt`, `AdaptiveCard`, or other SDK types. Reference-only — does not auto-load.
- **authentication** — Use this skill when the user is wiring authentication into a Microsoft Teams app — app-only bot auth, user OAuth/SSO via `signin.token-exchange` and `signin.verify-state`, Nested App Authentication (NAA) for tabs, or MSAL token acquisition for calling Microsoft Graph. Triggers on "Teams SSO", "signin.token-exchange", "AAD app Teams", "NAA Teams", "OAuth Teams bot".
- **cli-recipes** — Use this skill when the user is invoking the Microsoft Teams Developer CLI (`teams`) — scaffolding projects with `teams project new`, listing or updating apps with `teams app list/get/update`, checking auth status, or upgrading the CLI itself. Triggers on "teams CLI", "teams project new", "teams app update", "teams self-update", "teams status".
- **deployment** — Use this skill when the user is deploying a Microsoft Teams bot — sideloading the manifest, registering bot infrastructure with the Teams CLI, switching between localhost / devtunnel / production hosts, configuring sovereign clouds (US Gov, GCC High, China), or planning a Teams app release. Triggers on "deploy Teams bot", "sideload Teams app", "Teams app production", "Teams sovereign cloud", "Teams bot endpoint".
- **devtools** — Use this skill when the user is inspecting a Microsoft Teams bot locally — using `DevtoolsPlugin`, the activity inspector at `http://localhost:3979/devtools`, viewing MCP requests, or debugging incoming activities and outgoing responses. Triggers on "Teams DevTools", "DevtoolsPlugin", "localhost:3979", "activity inspector Teams", "Teams MCP inspector".
- **dialogs** — Use this skill when the user is building task module dialogs in Microsoft Teams — opening a modal from an Adaptive Card button, handling `dialog.open.<id>` and `dialog.submit.<id>`, returning a card or a web page as the dialog body, or chaining a multi-step dialog flow. Triggers on "Teams dialog", "task module", "modal in Teams", "dialog.open", "dialog.submit".
- **examples** — Use this skill when the user wants a worked, end-to-end scenario for a Microsoft Teams app — a full walkthrough for an echo bot, an AI quote agent, an Adaptive Card form, a message-extension search command, or an SSO + Microsoft Graph bot. Triggers on "Teams bot example", "full example Teams", "end-to-end Teams scenario", "echo bot example", "Teams SSO example".
- **getting-started** — Use this skill when the user wants to scaffold their first Microsoft Teams app or bot project, asks "how do I start a Teams bot in TypeScript", or needs to run a fresh `teams project new` template end-to-end — from CLI scaffold to a working echo bot reachable from a Teams client.
- **graph-integration** — Use this skill when the user is calling Microsoft Graph from a Microsoft Teams bot — listing or creating users / events / messages / files via `api.graph`, switching between app-only and on-behalf-of user tokens, or designing a Graph-driven feature. Triggers on "Microsoft Graph Teams", "api.graph", "list calendar events bot", "send mail Teams bot", "graph permissions".
- **mcp-plugin** — Use this skill when the user is integrating the Model Context Protocol with a Microsoft Teams app — exposing the Teams bot's tools as an MCP server with `McpPlugin`, consuming a remote MCP server from inside the bot, configuring `@microsoft/teams.mcp`, or wiring an A2A flow. Triggers on "Teams MCP", "MCP plugin Teams", "expose tools as MCP", "McpPlugin", "A2AClientPlugin".
- **message-extensions** — Use this skill when the user is building Microsoft Teams message extensions — search-based "compose extension" commands, action commands, link unfurling, or item selection from a search list. Triggers on "Teams message extension", "compose extension", "link unfurling", "message.ext.query", "search command Teams".
- **messaging** — Use this skill when the user is sending or receiving messages in a Microsoft Teams bot — handling incoming messages, replying, sending typing indicators, mentioning users, replying in threads, streaming AI responses, or pushing proactive notifications. Triggers on "Teams bot message", "send a reply", "typing indicator", "stream a response", "proactive message".
- **sdk-patterns** — Use this skill when the user is writing the core wiring of a Microsoft Teams app — booting the `App`, registering plugins, structuring activity routes, chaining middleware with `next()`, or organising the project layout for a TypeScript Teams bot or app. Triggers on phrases like "Teams App class", "Teams middleware", "register a plugin", "activity routing".
- **setup** — Use this skill when the user is preparing a development environment for Microsoft Teams app development — installing the Teams Developer CLI, verifying Node version, confirming a Teams sideloading-enabled tenant, or troubleshooting "command not found" / "teams CLI not installed" / "no Microsoft 365 dev tenant" errors before any other Teams work begins.
- **tabs** — Use this skill when the user is building a static-hosted tab inside a Microsoft Teams app — serving a web page from the bot's server via `app.tab()`, using the `@microsoft/teams.client` SDK to call back into Teams, configuring tab context, or wiring a personal / channel / dialog tab. Triggers on "Teams tab", "static tab", "configurable tab", "Teams client SDK".
- **troubleshoot** — Use this skill when the user is debugging a Microsoft Teams app problem — sideload failures, "bot did not reply", SSO `signin.token-exchange` errors, manifest validation, endpoint mismatches, Adaptive Card rendering issues, or any "why isn't my Teams bot working" question. Triggers on "Teams bot not responding", "sideload error", "Teams 401", "Teams 403", "Teams app manifest invalid".

## Custom commands

- `/add-feature` — Add a feature (message handler, Adaptive Card, dialog, message extension, tab, AI agent, MCP plugin, SSO, Graph call) to an existing Microsoft Teams app
- `/scaffold` — Scaffold a new Microsoft Teams bot, tab, AI agent, or Graph app with the Teams Developer CLI
