# AGENTS.md

## Project Overview

Public skills repository for WS Form. Pure content repo: no build tooling or package manager required. Skills are distributed via the [Vercel Skills CLI](https://github.com/vercel-labs/skills) (`npx skills add ws-form/skills --skill ws-form`).

Skills teach compatible AI coding agents how WS Form works: when to use native functionality, preferred workflows, architectural decisions, pitfalls, and best practices.

Skills complement the WS Form Abilities API and MCP Server. They do not replace them.

- Skills provide knowledge and workflow guidance.
- Abilities and MCP provide executable capabilities.

## Critical Warnings

- **`.cursor/`, `.agents/`, `.idea/` are PRIVATE.** Gitignored. Never commit. These are local editor and agent files.

## Skills Directory Structure

Skills live in `skills/` and must follow the [Agent Skills spec](https://agentskills.io/specification):

- **Name**: lowercase, hyphens only, 1-64 characters, no consecutive hyphens
- **Directory name must match** the `name` field in SKILL.md frontmatter
- **Required file**: `SKILL.md` with YAML frontmatter (`name`, `description`)
- **Optional**: `references/` subdirectory for supporting files

Keep `skills/ws-form/SKILL.md` concise. Put detailed guidance in `skills/ws-form/references/`. Link from SKILL.md to reference files. Do not nest references.

## Changelog

Record user-facing changes in `changelog.txt`. Add entries to the top version section in the same change as the content update.

- Version block header: `= X.Y.Z - MM/DD/YYYY =` using today's date, newest version on top
- Prefix each line with one of: `Added:`, `Changed:`, `Removed:`, `Bug Fix:`
- Put `Added:` entries first, then `Changed:`, then `Removed:`, then `Bug Fix:`
- Keep entries short, simple, and accurate
- Skip internal-only changes

## Content Rules

- Use actual WS Form terminology. Say Field ID 123, not Field 123. Say Conditional Logic. Say repeatable section, not repeater, unless the current product uses that term.
- Prefer native WS Form functionality over custom PHP or JavaScript in skill recommendations.
- Do not invent Ability names, tool names, field types, actions, or integrations.
- `form-create-json` and `field-add` accept only the type keys exposed by those tools. Do not document other native field types as Ability-creatable.
- WS Form URLs must end with a trailing slash and must not include tracking parameters.

## Code Style

- **Tabs** for indentation in code examples
