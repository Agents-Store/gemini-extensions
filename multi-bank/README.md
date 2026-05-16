# multi-bank (Gemini CLI Extension)

Multi-Bank Account Manager with broadcast architecture pattern. Aggregates financial data from Monobank and PrivatBank via MCP tools, broadcasts balance updates and budget alerts to subscribed components, categorizes transactions, and exports financial reports in CSV/PDF.

## Install

```bash
gemini extensions install agents-store/gemini-ext-multi-bank
```

## Required environment variables

Set in `~/.gemini/settings.json` or shell environment:

- `MONOBANK_MCP_URL`
- `PRIVATBANK_MCP_URL`

## Source

Canonical: https://github.com/agents-store/claude-public-plugins/tree/main/plugins/multi-bank
