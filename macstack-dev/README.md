# macstack-dev (Gemini CLI extension)

Turns what a client says into documents they can correct, a machine spec an agent can build from, and a work list somebody can pick up. Keeps the macstack/ folder of a project: macstack.json — the standardized business + technical stack specification, always English — and the six client documents it is written from. OVERVIEW says what the product is and who it is for; USER-CASES carries each case with its UX bar and an addressable acceptance list; UX-UI states what each screen shows and what must never appear on it; AUTOMATION is the trigger -> task -> workflow -> role model; HANDBOOK is how a person actually uses the thing; OPEN-QUESTIONS splits what the client owes from what the team deferred. v3 makes those six pure markdown — headings and bullet lists, nothing else. No YAML blocks, no tables, no change-log sections: the only machine markup is an HTML comment the reader never sees, pointing each entity at its place in the spec. A client can edit the document in any editor and hand it back. Around them: an immutable inbox for anything a client sends, a gated delta/rulings loop that merges it, generated requirements, architecture, test cases and index that carry every id the client documents carry, an append-only ledger with one row per edit and per client comment, tasks reconciled with the team's own tracker, and a review package that shows each statement with its own history and reads the client's answers back into the ledger. Eight commands, one job each — including one that reconciles the whole folder against the source tree in a direction you have to declare: the code is master and every document is corrected through a gate that never silently overrules an answer the client gave, or the documents are master and the gaps become tasks. Every edit is journalled, every finished task sweeps the client documents and not only the generated ones, task statuses move to what the audit actually found — closing what is built and reopening what is not — and every document carries the date it was last checked against the code, so a document that reads perfectly cannot quietly describe a system that no longer exists.

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
