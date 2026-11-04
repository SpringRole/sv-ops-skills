---
name: skill-builder
description: Turn a repeatable workflow into a reusable Pensieve skill. Guides the user through defining the skill's purpose, steps, required tools, and guardrails - then generates a ready-to-publish SKILL.md.
scope: company
---

# skill-builder - create a new Pensieve skill from a workflow

You help anyone at the org turn a repeatable workflow into a governed, reusable Pensieve skill. The output is a well-formed `SKILL.md` file that can be submitted for admin review and published org-wide.

## When this skill is invoked

The user wants to create a new skill. They might describe it as:
- "I keep doing X manually, can we make it a skill?"
- "Turn this into a skill"
- "Create a skill for [workflow]"
- "I want to automate [process]"

## Step 1 - Understand the workflow

Ask the user to describe the workflow in their own words. Gather:

1. **What does it do?** - one sentence summary (becomes the skill description)
2. **Who uses it?** - ops, engineering, everyone? (determines scope hint)
3. **What inputs does it need?** - IDs, names, dates, files, URLs
4. **What tools/APIs does it use?** - sv-mcp tools, Redash queries, Google Sheets, Slack, etc.
5. **What's the expected output?** - Slack message, sheet update, status change, etc.
6. **Any guardrails?** - things the skill must never do (e.g. mutate prod without confirmation)

If the user is vague, ask focused follow-up questions. Don't proceed until the workflow is clear.

## Step 2 - Identify required capabilities

From the workflow description, determine:

- **Tools used**: map to Pensieve tool names (e.g. `mcp__springverify__*`, `mcp__redash__*`, `mcp__slack__*`)
- **Egress needed**: any external API calls the skill makes
- **Scope**: `company` (org-wide, most common), `both` (org or personal), or `personal` (owner-only)
- **Credentials**: any env vars or service credentials referenced

## Step 3 - Draft the SKILL.md

Generate a complete SKILL.md following this structure:

```markdown
---
name: <kebab-case-name>
description: <one-line summary of what the skill does, max 200 chars>
scope: company
---

# <name> - <short description>

<1-2 sentence overview of what this skill automates and for whom.>

## Inputs

<List each input the user must provide, with type and example.>

## Steps

<Numbered steps the agent follows. Be specific - include exact tool names,
API endpoints, field names. Each step should be actionable.>

## Guardrails

<Things the skill must never do. Be explicit.>

## Output

<What the user sees when the skill completes.>
```

Rules for the generated SKILL.md:
- **name**: lowercase kebab-case, 2-4 words, descriptive (e.g. `insuff-clearance`, `daily-mis-report`, `hold-batch`)
- **description**: one sentence, under 200 characters, starts with a verb (e.g. "Clear insufficiency documents..." or "Generate daily MIS report...")
- **scope**: use `company` unless the skill is personal-only
- **Steps**: be specific about tool names and parameters. Don't use generic placeholders. If the skill calls `update_check_data_found`, say exactly which fields.
- **Guardrails**: always include "never mutate production data without explicit user confirmation" for any write-operation skill
- **No em-dash or en-dash**: use plain hyphens only
- **No verbose comments or history**: keep it clean

## Step 4 - Review with the user

Show the complete SKILL.md to the user. Ask:
- "Does this capture the workflow correctly?"
- "Any steps missing or wrong?"
- "Any additional guardrails needed?"

Iterate until the user approves.

## Step 5 - Save the skill

Once approved, save the SKILL.md to a local file:

1. Create directory: `sv-ops-skills/<skill-name>/`
2. Write the file: `sv-ops-skills/<skill-name>/SKILL.md`
3. If the skill needs supporting files (e.g. a Redash query template, a sample config), save those alongside the SKILL.md in the same directory.

Tell the user:
- The skill is saved locally at `sv-ops-skills/<skill-name>/SKILL.md`
- To make it available org-wide, it needs to be pushed to the `SpringRole/sv-ops-skills` repo and imported via the Skill Pack admin UI
- After import, an admin reviews, publishes, and promotes it - then it shows up for everyone

## Notes

- Skills are SKILL.md files with YAML frontmatter (--- fenced). The frontmatter parser supports: `name`, `description`, `scope`, `requiredCapabilities`, `egress`, `allowed-tools`, `declaredCreds`.
- `scope: company` or `scope: both` makes a skill eligible for org-wide import. `scope: personal` is excluded from org import.
- Setting `private: true` or including the agent-only marker in the body excludes the skill from org import.
- The skill name must be safe: lowercase alphanumeric + hyphens, no spaces or special characters.
- If the user's workflow references environment variables (like API keys in env vars), those become `declaredCreds` automatically during import.
- Keep skills focused - one skill per workflow. Don't combine unrelated operations.
