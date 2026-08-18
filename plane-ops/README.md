# plane-ops (Gemini CLI extension)

Plane Agile Ops knowledge plugin. Full coverage of the Plane MCP surface: sprint planning, task decomposition, estimation, backlog management, velocity tracking, retrospectives, standups, intake triage, modules, epics, initiatives, milestones, roadmaps, dependencies, burndown, pages (sprint reports, retros, ADRs, runbooks, specs, meeting notes), labels, workflow states, work item types, custom properties, comments, links, work logs, relations, history, bulk edits, search, members, and assignment. Tool- and instance-agnostic: works with any Plane MCP server or connector via a bootstrap skill that discovers tools across naming conventions.

## Status

Consumer access to the Gemini CLI closed on 2026-06-18; this extension is maintained for **enterprise Gemini Code Assist**, which still runs the Gemini CLI extension format (the consumer-facing successor is Antigravity CLI).

## Install

```bash
gemini extensions install https://github.com/Agents-Store/gemini-extensions
```

The [geminicli.com](https://geminicli.com) gallery — and the `install <url>` form above — only resolve a repository that carries `gemini-extension.json` at its **root**. This extension ships from the `agents-store-gemini-extensions` monorepo, where every plugin lives in its own subdirectory, so it will not appear in the gallery and the command above will not resolve directly. Until that repository is split one-plugin-per-repo, install locally instead:

```bash
git clone https://github.com/Agents-Store/gemini-extensions
gemini extensions link gemini-extensions/plane-ops
```

## Source

Canonical: https://github.com/agents-store/claude-public-plugins/tree/main/plugins/plane-ops
