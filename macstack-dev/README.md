# macstack-dev (Gemini CLI extension)

MACSTACK dev plugin for Agents Store. Creates and maintains the macstack/ folder of a Claude project: macstack.json — the standardized business + technical stack specification — plus the working documents around it: user cases per role, test cases derived from their acceptance bullets, milestones and tasks reconciled with the team's own task tracker, a typed development journal and its client-facing changelog, business logic in plain words, the decision log with cost-if-wrong, open questions split into what the client owes and what the team deferred, and an immutable inbox for client material. Init in existing projects, generate from scratch (result-first), discover context plugins and prototypes, scaffold project files in the prototype → stack plugins → dev plugins order, merge incoming client edits through a gated delta/rulings loop, report where the project stands and what to run next, wire Infisical env, install best-practice rules and commands.

## Status

Consumer access to the Gemini CLI closed on 2026-06-18; this extension is maintained for **enterprise Gemini Code Assist**, which still runs the Gemini CLI extension format (the consumer-facing successor is Antigravity CLI).

## Install

```bash
gemini extensions install https://github.com/Agents-Store/gemini-extensions
```

The [geminicli.com](https://geminicli.com) gallery — and the `install <url>` form above — only resolve a repository that carries `gemini-extension.json` at its **root**. This extension ships from the `agents-store-gemini-extensions` monorepo, where every plugin lives in its own subdirectory, so it will not appear in the gallery and the command above will not resolve directly. Until that repository is split one-plugin-per-repo, install locally instead:

```bash
git clone https://github.com/Agents-Store/gemini-extensions
gemini extensions link gemini-extensions/macstack-dev
```

## Source

Canonical: https://github.com/agents-store/claude-public-plugins/tree/main/plugins/macstack-dev
