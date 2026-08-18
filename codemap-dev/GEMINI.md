# codemap-dev

> Code understanding plugin for developers. Helps onboard to unfamiliar projects through beginner-friendly code review, step-by-step explanations, visual diagrams (architecture, ERD, flows) via drawio-mcp, and frontend testing via Playwright MCP.

Canonical source: https://github.com/agents-store/claude-public-plugins/tree/main/plugins/codemap-dev

## Skills

- **codemap-diagram** — This skill should be used when the user asks to "draw a diagram", "visualize architecture", "show me the database schema", "create an ERD", "sequence diagram", "flow diagram", "dependency graph", "architecture diagram", "C4 diagram", or needs any visual representation of code structure, data flow, or system architecture. All diagrams are generated as native mxGraph XML and rendered via drawio-mcp. Also triggers when a code explanation would benefit from a visual aid.

- **codemap-explain** — This skill should be used when the user asks to "explain this code", "what does this file do", "how does this work", "walk me through this function", "explain this module", "what is this for", "help me understand this", "break down this code for me", "give me a tour of this codebase", or needs a beginner-friendly step-by-step explanation of code, files, functions, or modules. Also triggers when the user points at code and asks "why" or "how" questions, or says "I don't understand this", "what's happening here", "trace this flow for me".

- **codemap-review** — This skill should be used when the user asks to "review code", "check this file", "what's wrong with this code", "review my PR", "code quality check", "find issues in this code", or wants feedback on readability, style, security, or common beginner mistakes. Provides structured review with "why" explanations, not just "what" fixes. Also triggers when a developer asks "is this code okay", "what can I improve", or "check my work".

- **codemap-examples** — This skill should be used when the user asks for "codemap examples", "how to use codemap", "show me what codemap can do", "codemap walkthrough", or wants to see end-to-end usage scenarios for the codemap plugin.

- **frontend-test** — This skill should be used when the user asks to "test the frontend", "check the UI", "explore the app in a browser", "test my admin panel", "find frontend bugs", "check for console errors", "verify the app works in a browser", "test user flows", "check the website", or wants a comprehensive frontend health report for a running application. Also triggers when the user says "open my app and test it", "browse my app", "check if the UI is working", or "give me a frontend report". Uses Playwright MCP to navigate, interact, and diagnose.


## Commands

- `/db` — Parse models, migrations, or schema and generate an ERD diagram + DB documentation
- `/diagram` — Generate a specific diagram — architecture, flow, db, sequence, deps
- `/explain` — Step-by-step explanation of a file, function, or module with mini-diagram if appropriate
- `/flows` — Find main user flows (entry points → services → DB) and visualize them
- `/onboard` — Generate a full onboarding report — README summary, stack, folder structure, entry points, how to run locally, and 3 main diagrams (architecture, main flow, DB)
- `/review` — Review a file, directory, or PR diff in beginner-friendly mode — structured feedback with "why" explanations
- `/test-frontend` — Test a running app's frontend — navigate pages, check UI, find errors, generate report
