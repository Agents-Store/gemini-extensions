# nocodb

> NocoDB database development plugin. Manage tables, records, columns, views, relations, formulas, rollups, lookups, filtering, sorting, search, aggregation, webhooks, and filter/sort management via MCP tools.

Canonical source: https://github.com/agents-store/claude-public-plugins/tree/main/plugins/nocodb

## Skills

- **advanced-queries** — Advanced data queries — search, aggregate, group by, complex filters. This skill should be used when the user asks to perform complex queries, aggregate data, group records, or search with advanced filter logic.
- **column-field-management** — Column/field types, relations, lookups, rollups, formulas. This skill should be used when the user asks to add columns, configure field types, set up relations, lookups, rollups, or formulas.
- **examples** — Tool call patterns, end-to-end workflow examples, and scenario references. This skill should be used when the user needs reference implementations, complete examples, or tool call patterns.
- **record-operations** — Record CRUD, filtering, sorting, bulk operations. This skill should be used when the user asks to create, read, update, or delete records, filter or search data, bulk import, or aggregate values.
- **schema-design** — Schema design best practices — entity modeling, relation patterns, creation order, formulas, views. This skill should be used when the user asks to design a database schema, plan tables and relationships, or build CRM/ERP/project databases.
- **table-management** — Table CRUD operations — create, list, get, delete tables. This skill should be used when the user asks to create a table, list existing tables, or manage table structure.
- **view-management** — View types — Grid, Kanban, Gallery, Form, Calendar. This skill should be used when the user asks to create or configure views, set up a kanban board, build a form, or manage view filters and sorts.
- **webhook-management** — Webhook management — create, list, delete, and test webhooks for table events. This skill should be used when the user asks to set up webhooks, configure event notifications, or integrate with external systems.

## Commands

- `/bulk-update` — Bulk update records in a NocoDB table
- `/create-column` — Add a column to a NocoDB table
- `/create-record` — Create a new record in a NocoDB table
- `/create-table` — Create a new table in NocoDB
- `/create-view` — Create a new view for a NocoDB table
- `/list-columns` — List columns in a NocoDB table
- `/list-records` — List records from a NocoDB table with optional filters
- `/list-tables` — List all tables in the NocoDB base
- `/list-views` — List views for a NocoDB table
- `/search-records` — Search records in a NocoDB table
