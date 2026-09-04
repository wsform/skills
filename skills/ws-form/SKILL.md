---
name: ws-form
description: >-
  Build, edit, and troubleshoot forms in WordPress with WS Form. Use when
  creating or modifying WS Form forms; choosing field types; configuring layout,
  tabs, sections, or repeatable sections; adding Conditional Logic, calculations,
  actions, or integrations; working with submissions, validation, spam
  protection, or Styler; using the WS Form Abilities API or MCP Server; or
  deciding whether custom PHP or JavaScript is necessary. Prefer native WS Form
  capabilities over custom implementations.
license: GPL-3.0-or-later
metadata:
  author: WS Form
  documentation: https://wsform.com/knowledgebase/
---

# WS Form

Teach the agent to behave like an experienced WS Form developer.

This skill is knowledge and workflow guidance. The WS Form Abilities API and MCP Server are the executable tools. Discover those tools at runtime. Do not invent tool names.

## Core principle

Prefer native WS Form functionality over custom implementation whenever WS Form already provides the required capability.

Before writing custom PHP, JavaScript, AJAX handlers, REST endpoints, database tables, validation systems, submission storage, spam protection, payment processing, or third-party API integration code, determine whether WS Form already provides the required functionality.

Custom code is generally appropriate only when:

1. WS Form does not provide the required capability.
2. The user explicitly requests custom implementation.
3. A documented WS Form extension mechanism is the correct solution.

If the user says to build a form without plugins, respect that request. Do not force WS Form merely because this skill is installed.

## Do not

- Directly modify WS Form database tables (`wsf_*`)
- Reimplement WS Form functionality
- Create custom submission storage when WS Form submissions are appropriate
- Create custom validation when native validation is sufficient
- Write custom conditional JavaScript when Conditional Logic is sufficient
- Write custom integration code where WS Form already provides an appropriate action or add-on
- Store secrets in frontend JavaScript
- Depend on undocumented WS Form internals
- Rewrite an entire form when a granular Ability exists for the requested change
- Assume form, field, section, action, conditional, or row identifiers
- Make destructive changes without first inspecting the current form or configuration
- Change a field's type in place (type is immutable)
- Pass field type keys that are not in the live `field-add` type list
- Create a Styler style unless the user asks for a color theme, branding, or visual appearance
- Treat email notifications as form settings (they are Send Email actions)

## Preferred workflow

1. Determine whether WS Form is installed or relevant.
2. Inspect the existing environment before changing anything.
3. Discover currently available WS Form MCP tools / WordPress Abilities when connected to a site.
4. Inspect the existing form before modifying it.
5. Determine whether WS Form already supports the requested behavior.
6. Prefer native WS Form functionality.
7. Prefer granular WS Form Abilities for targeted changes where available.
8. Use larger form-level operations only when necessary.
9. Use documented extension mechanisms only when native functionality cannot satisfy the requirement.
10. Verify the resulting form or configuration.

New forms created through Abilities start as drafts. Publish with `ws-form/form-publish` before embedding on a live page.

Write operations that change form structure require **Allow Updates** (WS Form → Settings → AI, default off). Deletes require **Allow Deletes** (default off). If a write tool is missing, inspect those settings rather than inventing an alternative tool.

## Abilities and MCP

Ability names use the `ws-form/` namespace (for example `ws-form/forms`). MCP exposes those Abilities as tools. The tools actually available depend on edition (LITE vs PRO), settings, Styler/framework, and whether the MCP Adapter is present.

Discover tools at runtime. Do not assume the full set is available.

Preferred patterns:

| Task | Prefer |
|---|---|
| Find forms | `ws-form/forms` |
| Create a form | `ws-form/form-create-json`, then `ws-form/form-publish` if it should be live |
| Inspect a form | `ws-form/form-get-json`, or `ws-form/fields` / `ws-form/sections` / `ws-form/tabs` for IDs |
| Change one field label or meta | `ws-form/field-update` |
| Add a field | `ws-form/field-add` |
| Change one action or notification | `ws-form/actions` then `ws-form/action-update` |
| Change one Conditional Logic rule | `ws-form/conditionals` then `ws-form/conditional-update` |
| Whole-form JSON replace | `ws-form/form-update-json` only when the change is not a granular field/action/conditional edit. Keep the existing groups/sections/fields structure. |
| Embed | `ws-form/form-shortcode` or `ws-form/form-block` |

For action and conditional rows, prefer `row_id` (stable). Use `index` or unique `label_match` only if `row_id` is unknown.

Refer to identifiers as **Field ID 123**, not Field 123.

Details: [references/abilities-and-mcp.md](references/abilities-and-mcp.md)

## Read these references when relevant

- Forms, create vs update, identifiers: [references/forms.md](references/forms.md)
- Field types Abilities can create: [references/fields.md](references/fields.md)
- Tabs, sections, grid, repeatable sections: [references/sections-and-layout.md](references/sections-and-layout.md)
- Show/hide and related behavior: [references/conditional-logic.md](references/conditional-logic.md)
- `#calc()` and variables: [references/calculations.md](references/calculations.md)
- Email, database, webhooks, add-ons: [references/actions-and-integrations.md](references/actions-and-integrations.md)
- Submission records, notes, stats: [references/submissions.md](references/submissions.md)
- Native and server-side validation: [references/validation.md](references/validation.md)
- Honeypot, captcha, Akismet, IP/keyword blocks: [references/spam-protection.md](references/spam-protection.md)
- Styler, CSS variables, frameworks: [references/styling.md](references/styling.md)
- Diagnostic-first debugging: [references/troubleshooting.md](references/troubleshooting.md)

## Native-first decision tests

- Repeating product rows: native **repeatable section**, not custom JS. Current Abilities cannot create that; use the layout editor. Do not `field-add` `section_add` or similar.
- Show a field when another field equals a value: use **Conditional Logic**, not custom JS.
- Send leads to Salesforce (or another advertised integration): check whether the official WS Form add-on is installed. Prefer that action over a custom REST client.
- Change the label on Field ID 123: inspect, then `ws-form/field-update`. Do not rewrite the form JSON.
- Form is not submitting: inspect the form, the failing action, submissions, and debug output. Do not replace submission with custom PHP.

## Documentation

- [Abilities](https://wsform.com/knowledgebase/abilities/)
- [MCP Server](https://wsform.com/knowledgebase/mcp-server/)
- [Knowledge Base](https://wsform.com/knowledgebase/)
