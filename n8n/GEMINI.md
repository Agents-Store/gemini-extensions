# n8n

> n8n workflow automation plugin. Manage workflows, execute automations, configure nodes, handle credentials, monitor executions, expression syntax, node configuration patterns, and code node best practices via MCP tools.

Canonical source: https://github.com/agents-store/claude-public-plugins/tree/main/plugins/n8n

## Skills

- **code-patterns** — Code node patterns — JavaScript and Python data transformation, API processing, date handling, binary data. This skill should be used when the user asks to write code in Code nodes, transform data with JavaScript or Python, or process binary data.
- **credential-tag-management** — Credentials and tags management. This skill should be used when the user asks to create or manage credentials, list service connections, organize workflows with tags, or configure authentication.
- **examples** — Tool call patterns, end-to-end workflow examples, and scenario references. This skill should be used when the user needs reference implementations, complete examples, or tool call patterns.
- **execution-monitoring** — Workflow execution, monitoring, and debugging. This skill should be used when the user asks to run a workflow, check execution status, view execution history, or debug workflow errors.
- **expression-syntax** — Expression syntax — variables, methods, JMESPath, data references. This skill should be used when the user asks to write expressions, reference data between nodes, use JMESPath, or debug expression errors.
- **node-configuration** — Node configuration — HTTP Request, Code, IF, Switch, Merge, Split In Batches, error handling. This skill should be used when the user asks to configure a specific node type, set up authentication, write conditions, or add error handling.
- **workflow-creation** — Workflow creation — node definitions, connections, triggers, workflow structure. This skill should be used when the user asks to create a new workflow, build an automation, set up triggers, or scaffold a workflow structure.
- **workflow-editing** — Workflow editing — adding/removing nodes, updating connections, modifying node parameters. This skill should be used when the user asks to edit, modify, or update an existing workflow, add or remove nodes, or change node settings.

## Commands

- `/activate-workflow` — Activate or deactivate an n8n workflow
- `/create-workflow` — Create a new n8n workflow
- `/debug-workflow` — Debug a failed n8n workflow execution
- `/execute-workflow` — Execute an n8n workflow
- `/get-workflow` — Get n8n workflow details
- `/list-credentials` — List available n8n credentials
- `/list-executions` — List recent workflow executions
- `/list-tags` — List workflow tags
- `/list-workflows` — List all n8n workflows
