# plane-ops

> Plane Agile Ops knowledge plugin. Full coverage of the Plane MCP surface: sprint planning, task decomposition, estimation, backlog management, velocity tracking, retrospectives, standups, intake triage, modules, epics, initiatives, milestones, roadmaps, dependencies, burndown, pages (sprint reports, retros, ADRs, runbooks, specs, meeting notes), labels, workflow states, work item types, custom properties, comments, links, work logs, relations, history, bulk edits, search, members, and assignment. Tool- and instance-agnostic: works with any Plane MCP server or connector via a bootstrap skill that discovers tools across naming conventions.

Canonical source: https://github.com/agents-store/claude-public-plugins/tree/main/plugins/plane-ops

## Skills

- **agile-fundamentals** — Single source of truth for Agile formulas, Definition of Ready, Definition of Done, MoSCoW priority mapping, WSJF scoring, capacity and velocity formulas, WIP limits, Fibonacci story points, and sprint buffer policy. Use whenever the user asks about story points, estimation, velocity, capacity, focus factor, WSJF, DoR/DoD, MoSCoW, Fibonacci, sprint buffer, or any Agile rule — and whenever any other plane-ops skill, command, or agent needs canonical Agile definitions. Do not duplicate these rules elsewhere — link here instead.
- **backlog-management** — Backlog management — MoSCoW prioritization, WSJF scoring, value-effort analysis, backlog health monitoring, grooming workflows. Use when prioritizing backlog, grooming items, or assessing backlog health.
- **connector-bootstrap** — MUST be consulted at the start of ANY Plane-related request before answering the user or declaring tools unavailable. Discovers Plane tools across any MCP server, connector, or instance naming convention (cowork mode, remote MCP, local MCP, self-hosted, cloud). Use when the user mentions sprint, backlog, work item, cycle, epic, module, milestone, initiative, project, issue, ticket, task, standup, retro, estimate, roadmap, board, Plane, or any work-management operation — even if Plane tools are not visible yet. Also use before saying "I don't have access to Plane tools" or "tool not available".
- **daily-standup** — Daily standup support — progress summary, blocker identification, team status updates, sprint progress tracking, async standup. Use when running daily standups, checking team progress, identifying blockers, or generating async standup reports.
- **epics-initiatives-milestones** — Long-horizon planning in Plane — epics, initiatives, and milestones. Use when the user wants to create an epic, group epics under an initiative, track a release milestone, build a roadmap, or report progress on a long-running goal that spans multiple sprints or modules. Clarifies the difference between the three and when to use which.
- **estimation** — Estimation — story points, Fibonacci scale, t-shirt sizing, relative estimation, planning poker facilitation. Use when estimating work items or running estimation sessions.
- **examples** — Tool call patterns, end-to-end workflow examples, and scenario references for Plane Agile workflows. Use when needing reference implementations, complete examples, or tool call patterns.
- **intake-triage** — Plane intake inbox — triage incoming requests, bug reports, and ideas before they enter the backlog. Use when the user wants to review the intake queue, accept or reject incoming items, convert intake items into work items, or set up a triage process. Covers daily/weekly triage rituals and routing rules.
- **labels-states-properties** — Design taxonomies for Plane projects — labels, workflow states, work item types, and custom properties. Use when the user asks how to organize labels, design a state machine, set up custom fields, configure work item types, decide between label/property/type, or audit a noisy taxonomy. Covers naming conventions, recommended sets, and the difference between the four metadata mechanisms.
- **modules** — Plane modules — feature and workstream grouping that cuts across sprints. Use when the user wants to group related work items by feature area, track progress of a larger feature over multiple sprints, create or manage modules, or ask about workstream progress. Modules complement cycles (time-boxed) by organizing work around outcomes (scope-boxed).
- **pages-publishing** — Create and publish Plane pages — sprint reports, retro notes, release notes, roadmap, meeting notes, decision logs (ADRs), specs, runbooks, and any general-purpose page as formatted HTML. Use when the user wants to publish a report, write a sprint summary, create a retro page, generate release notes, share a roadmap, document a decision, write a spec, capture meeting notes, or create any Plane page. Includes reusable HTML templates and Plane editor compatibility rules.
- **project-setup** — Agile-ready project setup — states, labels, work item types, properties, and feature configuration for Agile workflows. Use when setting up a new project or configuring an existing project for Agile.
- **sprint-planning** — Sprint planning — capacity calculation, sprint goal setting, work item selection, cycle creation. Use when planning a new sprint, calculating capacity, or selecting items for a sprint.
- **sprint-review-retro** — Sprint review and retrospective — completion metrics, previous retro action review, Start-Stop-Continue retro, DAKI format, 4Ls format, action item tracking, sprint close. Use when running sprint review, retrospective, closing a sprint, or reviewing previous retro action item status.
- **task-decomposition** — Task decomposition — INVEST criteria, vertical slicing, epic-to-subtask breakdown, story splitting patterns. Use when breaking down epics, stories, or large work items into smaller pieces.
- **velocity-metrics** — Velocity and metrics — historical velocity calculation, sprint burndown analysis, WIP limits, throughput tracking, time effort analysis. Use when calculating velocity, analyzing sprint progress, reviewing team metrics, comparing estimated vs actual effort, or checking time logged per story point.
- **work-items** — Work item operations in Plane — create, read, update, delete, search, assign, label, link, comment, relate, and log time on work items (issues/tasks/tickets). Use when the user wants to create a task, update an issue, add a comment, attach a label, link a PR, set a blocker, log work time, or inspect a specific item. Covers work item types, custom properties, and multi-item batch edits.

## Commands

- `/assign` — Assign or unassign a Plane work item to one or more users
- `/backlog-health` — Analyze backlog health — unestimated items, stale items, missing details
- `/bulk-update` — Apply the same change to many Plane work items at once
- `/burndown` — Show sprint burndown and projected completion for the active cycle
- `/close-sprint` — Close current sprint — review completion, transfer incomplete items, archive
- `/comment` — Add or list comments on a Plane work item
- `/create-epic` — Create a Plane epic for a large feature spanning multiple sprints
- `/create-module` — Create a Plane module (feature/workstream grouping across sprints)
- `/create-sprint` — Create a new sprint cycle in Plane
- `/cycles` — Manage Plane cycles beyond planning — list, archive, transfer, delete
- `/decompose` — Break down a work item into smaller stories/tasks using INVEST criteria
- `/dependencies` — Show dependency graph and active blockers for a project or sprint
- `/epic` — Manage Plane epics — list, get, update, delete (complement to /create-epic)
- `/estimate` — Estimate unestimated work items using Fibonacci story points
- `/find` — Search Plane work items by query, identifier, or filter
- `/groom-backlog` — Run a backlog grooming session — refine, estimate, and prioritize
- `/history` — Show the change history (activity log) of a Plane work item
- `/initiative` — Manage Plane initiatives — strategic groupings above epics
- `/label` — Manage Plane labels — list, create, update, delete, or apply to a work item
- `/link` — Add or list external links (PRs, docs, designs) on a Plane work item
- `/log-time` — Log work time on a Plane work item (create a work log entry)
- `/members` — List Plane workspace or project members and their roles
- `/milestone-status` — Show status and risk for a Plane milestone (release/launch tracking)
- `/milestone` — Manage Plane milestones — create, list, add items, update, delete
- `/module` — Manage Plane modules — list, get, update, add items, archive (complement to /create-module)
- `/my-work` — List Plane work items assigned to the current user
- `/page` — Create, list, get, or update a Plane page (project or workspace scope)
- `/plan-sprint` — Plan a new sprint — calculate capacity, select items from backlog, set sprint goal
- `/projects` — List, inspect, update, or delete Plane projects (complement to /setup-project)
- `/property` — Manage custom properties on Plane work items (severity, customer impact, etc.)
- `/publish-report` — Publish a Plane page — sprint report, retro, release notes, or roadmap
- `/relate` — Create a relation between two Plane work items (block, duplicate, relates-to)
- `/retro` — Run a sprint retrospective and create action items
- `/roadmap` — Generate a Now / Next / Later roadmap view across cycles, modules, epics
- `/setup-project` — Set up a new Agile-ready project with recommended states, labels, and work item types
- `/sprint-status` — Show current sprint status — progress, burndown, at-risk items
- `/standup` — Generate daily standup summary for current sprint
- `/state` — Manage Plane workflow states — list, create, update, delete, reorder
- `/triage-intake` — Triage the Plane intake inbox — accept, defer, or reject incoming items
- `/velocity` — Calculate team velocity from historical sprint data
- `/whoami` — Show the current Plane user, workspace, and connector status
- `/work-item-type` — Manage custom work item types in a Plane project (Story, Bug, Spike, ...)
- `/work-item` — Create, update, or inspect a work item (issue/task/ticket) in Plane
- `/wsjf-prioritize` — Prioritize backlog items using Weighted Shortest Job First (WSJF) scoring
