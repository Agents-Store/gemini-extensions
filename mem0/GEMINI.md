# mem0

> Mem0 memory management plugin. Store, search, update, and organize memories with semantic search, batch operations, file attachments, and change history tracking via MCP tools.

Canonical source: https://github.com/agents-store/claude-public-plugins/tree/main/plugins/mem0

## Skills

- **examples** — Tool call patterns, end-to-end workflow examples, and scenario references. This skill should be used when the user needs reference implementations, complete examples, or tool call patterns.
- **file-management** — File management — attach files to memories and search file content via vector search. This skill should be used when the user asks to upload documents, attach files, or search within attached files.
- **history-tracking** — Memory history and change tracking — view evolution of memories over time, audit modifications, and track knowledge changes. This skill should be used when the user asks to see memory changes, audit modifications, or track how information evolved.
- **memory-crud** — Memory CRUD operations — add, get, update, delete memories, and batch operations. This skill should be used when the user asks to create, read, update, or delete memories, or perform bulk memory management.
- **search-retrieval** — Search and retrieval — semantic search, listing, filtering, and relevance tuning. This skill should be used when the user asks to find memories, search knowledge, list stored information, or tune search results.

## Commands

- `/add-memory` — Add a new memory from text
- `/attach-files` — Attach files to an existing memory
- `/batch-delete` — Batch delete multiple memories at once (up to 100)
- `/batch-update` — Batch update multiple memories at once (up to 100)
- `/delete-memory` — Delete a memory by its ID
- `/get-memory` — Get a specific memory by its ID
- `/list-memories` — List all stored memories with optional pagination
- `/memory-history` — View the change history of a memory
- `/search-files` — Search attached files via vector search
- `/search-memories` — Search memories by semantic query
- `/update-memory` — Update an existing memory's content
