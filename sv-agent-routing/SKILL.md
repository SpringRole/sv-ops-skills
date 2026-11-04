---
name: sv-agent-routing
description: Think-first routing for SpringVerify tasks. Always check available MCP tools and skills before attempting any task - never hand-roll what a connector already provides.
scope: company
---

# sv-agent-routing - MCP-first, skill-first approach

You are working inside SpringVerify's operations platform. Before attempting ANY task, follow this routing discipline.

## Rule 1: Check tools FIRST, act SECOND

Before writing any script, automation, or workaround:

1. Run `tool_search` with keywords matching the task (e.g. "check data found", "upload file", "insufficiency", "candidate", "redash")
2. Check the Connectors list in your context for relevant MCP tools
3. Run `list_skills` or `search_skills` to find a skill that matches the task

If a matching MCP tool or skill exists, USE IT. Never hand-roll browser automation, API calls, or scripts for something an existing tool already does.

## Rule 2: Known MCP connectors and their capabilities

These connectors are available. Search for specific tools within them:

- **SpringVerify** (`springverify`): Full CRUD for candidates, checks, companies, tasks, effort logs, insufficiencies, vendor requests, billing, reports. Includes `upload_file`, `update_check_data_provided`, `update_check_data_found`, `tag_document`. ~370 tools covering all check types (address, education, employment, court, reference, custom).
- **Redash** (`redash`): Query execution, dashboard management, visualization. Use `execute_query` with existing query IDs. Always use `dataSourceId=5` (SVIN/rds_mysql).
- **Slack** (`slack`): Read channels/threads, search messages, send drafts. Never use `slack_send_message` - always `slack_send_message_draft`.
- **Google Apps Script** (`google-apps-script`): Manage GAS projects, get/update content, create deployments, run functions.
- **Google Sheets** (`google-sheets`): Read/write spreadsheet data.
- **Google Slides** (`google-slides`): Create/update presentations.
- **Azure DevOps** (`azure-devops`): Work items, repos, pipelines, PRs.
- **n8n** (`n8n`): Workflow automation nodes and templates.

## Rule 3: Never hand-roll browser automation

If a task requires browsing a website:
1. Check if a `browse` skill exists - load it first
2. If browse requires a managed browser provider and none is configured, STOP and report: "This task needs a managed browser provider (Kernel/Anchor/Browserbase). Please configure one in Admin -> Service credentials."
3. NEVER write raw Playwright/Puppeteer/Selenium scripts
4. NEVER attempt to solve CAPTCHAs
5. NEVER type passwords or credentials into any sign-in page - report `SIGNIN_NEEDED <url>` instead

## Rule 4: Credential errors are terminal

If you get "credential handle is not available on this turn":
- This is an ACL rejection, not a transient error
- Do NOT retry the same call
- Report the error and suggest the admin widen the credential grant for this scope

## Rule 5: Use existing Redash queries

Before writing raw SQL:
1. Search for existing Redash queries that match the task (`search_queries` or `list_queries`)
2. If a query exists, execute it with parameters - don't rewrite it
3. If you must write new SQL, always use `dataSourceId=5` (never 2)

## Rule 6: SV API conventions

When using SpringVerify MCP tools:
- Table is `company` (singular), not `companies`
- `candidates` has `name` (not first_name/last_name), no `company_id` column
- `candidates_address` has `address_1`, `address_2` (not `address`)
- PAV check: `check_name_id_fk = 1`
- Company ID comes from effort logs `company_id_fk`, not candidates table
- Status codes: 0=RTI, 1=Verified, -1=Insufficiency, 4=In Progress, 5=On Hold, 7=N/A
- Document URLs need signed URL via `get_signed_url` - raw `document_url` returns 403

## Rule 7: Efficiency

- Don't call `get_check_data_provided` in a loop for hundreds of checks - ask if a batch endpoint exists
- Don't retry failed tool calls more than twice - search for an alternative approach
- If a task is taking more than 5 minutes of model time with no progress, stop and reassess your approach
