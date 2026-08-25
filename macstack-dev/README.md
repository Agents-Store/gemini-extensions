# macstack-dev (Gemini CLI extension)

Turns what a client says into documents they can correct, a machine spec an agent can build from, and a work list somebody can pick up. Keeps the macstack/ folder of a project: macstack.json — the standardized business + technical stack specification, always English — and the six client documents it is written from. OVERVIEW says what the product is and who it is for; USER-CASES carries each case with its UX bar and an addressable acceptance list; UX-UI states what each screen shows and what must never appear on it; AUTOMATION is the trigger -> task -> workflow -> role model; HANDBOOK is how a person actually uses the thing; OPEN-QUESTIONS splits what the client owes from what the team deferred. Around them: an immutable inbox for client material, a gated delta/rulings loop that merges it, generated architecture, test cases and index, a typed development journal with its client-facing changelog, milestones and tasks reconciled with the team's own tracker, and a review package every claim of which has a place to answer. v2 replaces column-position parsing with anchors and YAML blocks, so a document stops being a grid a client cannot correct: entities carry ids, machine fields live in one fenced block, prose lives in anchored sections, and tables are held to a budget lint measures. Seven commands instead of seventeen. Every edit journals, every finished task sweeps the documents, and every document carries the date it was last checked against the code — so a document that reads perfectly cannot quietly describe a system that no longer exists.

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
