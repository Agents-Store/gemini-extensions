# deep-research

> Deep Research plugin. Comprehensive web research using 4 providers (Exa, Firecrawl, Jina, Perplexity) with capability-based CONNECTORS pattern and automatic FALLBACK chains. Search, scrape, crawl, extract — each action tries multiple providers until one succeeds.

Canonical: https://github.com/agents-store/claude-public-plugins/tree/main/plugins/deep-research

## Available skills

Skills under `skills/` auto-load by description match:

- **content-extraction** — Content reading and extraction guidelines — reading URLs, scraping pages, crawling sites, extracting PDFs, and taking screenshots. Use when reading web content, extracting structured data from pages, or processing documents.
- **deep-research** — Main research automation skill. 7-step algorithm for comprehensive research with 6 research types, query planning, parallel search, extraction, synthesis, and structured reporting. Use when conducting any multi-step research task.
- **examples** — Tool call patterns, end-to-end research workflow examples, and scenario references for all 6 research types. Use when you need reference implementations or complete research examples.
- **report-generation** — Report templates and generation guidelines — Executive Summary, Deep Research Report, and Comparison Table formats with methodology and citation rules. Use when formatting research results into structured reports.
- **search-strategies** — Search strategy guidelines — tool selection, fallback chains, query optimization, and parallel search orchestration. Use when choosing which search tools to use, handling tool failures, or optimizing search queries.

## Custom commands

- `/compare` — Compare multiple products, companies, or technologies
- `/crawl-site` — Crawl an entire website or map its structure
- `/read-url` — Read and extract content from a URL with fallback
- `/research` — Conduct comprehensive deep research on a topic using 7-step algorithm
- `/search` — Search the web using optimal provider with automatic fallback
- `/summarize` — Summarize a topic or URL content
