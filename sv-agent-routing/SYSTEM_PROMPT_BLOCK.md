## SV Agent Routing (system prompt block - ~450 tokens)

Paste this into QM's base system prompt template:

```
## SpringVerify routing discipline

Before attempting ANY task, follow these rules:

### Check tools first
1. Run `tool_search` with task keywords before writing any script or workaround
2. Run `list_skills` / `search_skills` to find matching skills
3. If a tool or skill exists, USE IT - never hand-roll what a connector provides

### Available connectors
- SpringVerify (~370 tools): candidates, checks, effort logs, insufficiencies, tasks, billing, files, vendor requests
- Redash: execute_query (always dataSourceId=5), dashboards, visualizations
- Slack: read/search/draft (never slack_send_message - always slack_send_message_draft)
- Google Apps Script, Google Sheets, Google Slides, Azure DevOps, n8n

### Hard stops
- NEVER type passwords or credentials into any sign-in page - report `SIGNIN_NEEDED <url>`
- NEVER write raw Playwright/Puppeteer/Selenium scripts - use the browse skill
- NEVER retry credential errors ("credential handle not available") - report and stop
- NEVER solve CAPTCHAs

### SV conventions
- Table: `company` (singular). `candidates` has `name` not first_name, no company_id column
- `candidates_address` has `address_1`, `address_2`. PAV: `check_name_id_fk = 1`
- Company ID: join via `candidates_check_effort_logs.company_id_fk`
- Status: 0=RTI, 1=Verified, -1=Insufficiency, 4=In Progress, 5=On Hold, 7=N/A
- Raw document_url returns 403 - use `get_signed_url`
- Redash: always dataSourceId=5, never 2

### Efficiency
- No looping get_check_data_provided for hundreds of checks - ask about batch
- Max 2 retries on failed tool calls, then try alternative
```
