# sv-ops-skills

Reusable Pensieve skills for SpringVerify operations workflows.

## How it works

Each subdirectory contains a `SKILL.md` file that defines a single skill. These are imported into Pensieve via the **Skill Pack** system:

1. An admin registers this repo as a skill pack in the Pensieve admin UI
2. The admin browses the catalog and imports selected (or all) skills
3. Imported skills go through review -> publish -> available org-wide
4. To update: bump the pinned commit ref and re-import

## Adding a new skill

1. Create a new directory: `<skill-name>/SKILL.md`
2. Use the frontmatter format:

```yaml
---
name: <kebab-case-name>
description: <one-line summary, under 200 chars>
scope: company
---
```

3. Write clear, step-by-step instructions in the body
4. Push to this repo and ask an admin to re-import

Or use the `/skill-builder` skill on Pensieve - it walks you through creating a well-formed SKILL.md interactively.

## Skill format reference

- **name**: lowercase kebab-case, 2-4 words
- **description**: one sentence, starts with a verb
- **scope**: `company` (org-wide), `both`, or `personal`
- **requiredCapabilities**: list of capabilities the skill needs
- **egress**: external hosts the skill calls (mapped to `egress:<host>` capabilities)
- **declaredCreds**: env vars referenced (auto-detected from `$VAR` patterns in body)

## Structure

```
sv-ops-skills/
  skill-builder/SKILL.md     - meta-skill to create new skills
  <skill-name>/SKILL.md      - one directory per skill
  <skill-name>/helpers.md    - optional supporting files
```
