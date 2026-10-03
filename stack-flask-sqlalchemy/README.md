# stack-flask-sqlalchemy (Gemini CLI extension)

Flask + SQLAlchemy architecture plugin. How the application factory, the Flask-SQLAlchemy session, Alembic migrations and Jinja2 templates fit together: where db is created, the app context, the transaction boundary, expire_on_commit, N+1 at the route-to-template edge, and a full-feature recipe. Tool knowledge comes from its dependencies flask-dev and sqlalchemy-dev.

## Status

Consumer access to the Gemini CLI closed on 2026-06-18; this extension is maintained for **enterprise Gemini Code Assist**, which still runs the Gemini CLI extension format (the consumer-facing successor is Antigravity CLI).

## Install

```bash
gemini extensions install https://github.com/Agents-Store/gemini-extensions
```

The [geminicli.com](https://geminicli.com) gallery — and the `install <url>` form above — only resolve a repository that carries `gemini-extension.json` at its **root**. This extension ships from the `agents-store-gemini-extensions` monorepo, where every plugin lives in its own subdirectory, so it will not appear in the gallery and the command above will not resolve directly. Until that repository is split one-plugin-per-repo, install locally instead:

```bash
git clone https://github.com/Agents-Store/gemini-extensions
gemini extensions link gemini-extensions/stack-flask-sqlalchemy
```

## Source

Canonical: https://github.com/agents-store/claude-public-plugins/tree/main/plugins/stack-flask-sqlalchemy
