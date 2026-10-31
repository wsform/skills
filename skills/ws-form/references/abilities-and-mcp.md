# Abilities and MCP

WS Form registers WordPress Abilities in the `ws-form` category. Names are `ws-form/{id}`.

The MCP server exposes those Abilities as MCP tools. This skill does not execute them. Discover the tools available on the connected site at runtime. Do not invent names.

Documentation: [Abilities](https://wsform.com/knowledgebase/abilities/), [MCP Server](https://wsform.com/knowledgebase/mcp-server/).

The live tools on the connected site remain authoritative. Ability input/output schemas live on those tools. Do not copy stale schemas into prompts when the live tool description is available.

## How MCP relates to Abilities

1. WS Form registers Abilities with the WordPress Abilities API.
2. If the [MCP Adapter](https://wsform.com/knowledgebase/mcp-server/) is present and **Enable** is on under WS Form → Settings → AI, WS Form creates an MCP server named `ws-form`.
3. REST route: `{site}/wp-json/ws-form/v1/mcp` (shown in WS Form → Settings → AI as the server URL).
4. Authentication and WS Form capabilities still apply.
5. **Include in MCP Discovery** controls whether clients can discover the abilities. It does not disable permission checks.

Official docs currently describe WordPress 7 and the MCP Adapter plugin as prerequisites. Verify the target site rather than assuming a version.

Angie (Elementor) can consume WS Form Abilities when that integration is present. That is not a different set of product capabilities.

## Settings that change the tool list

| Setting | Default | Effect |
|---|---|---|
| Enable (MCP Adapter) | off | Registers the MCP server |
| Include in MCP Discovery | on | MCP clients can discover abilities |
| Allow Updates | **off** | Structure writes (`form-update-json`, field/tab/section/action/conditional add, update, and delete) and style palette, assignment, or default changes |
| Allow Deletes | **off** | `form-delete` and `style-delete` |

`form-create-json`, publish, draft, clone, restore, form and style lock/unlock, and form-folder tools stay available when those checkboxes are off. They still require the matching WordPress capability (`create_form`, `edit_form`, `publish_form`, `delete_form`, `lock_form`, `unlock_form`, `lock_form_style`, `unlock_form_style`, and the style equivalents).

If read tools work and a structure write is missing, check Allow Updates. If `form-delete` or `style-delete` is missing, check Allow Deletes. `style-create` can make an empty style without Allow Updates. Setting palette colors, assigning `form_id`, or setting `default` requires Allow Updates.

Style abilities register only when Styler is enabled (framework `ws-form`). Action and Conditional Logic abilities are PRO.

## Discover tools at runtime

When connected:

1. List MCP tools, or list WordPress Abilities in category `ws-form`.
2. Read each tool's description and schema. Several descriptions already contain agent instructions (prefer `field-update` over JSON rewrite, Send Email is an action, do not create styles unless asked). Follow those.
3. Do not call a name from this file if it is not in the live list.

Treat the live registry as truth. The tools on the connected site can differ from this file.

## Current Ability groups

Namespace prefix `ws-form/` is omitted in the tables for readability. The live name is `ws-form/forms`, not `forms`.

### Forms

| Ability | Role |
|---|---|
| `forms` | List (optional `published`, `order_by`, `order`, `folder_id`) |
| `form-folders` | List folders (`id`, `label`, `parent_id`, direct `form_count`) |
| `form-folder-create` | Create. `parent_id` `0` is a root folder. Depth limit 5. Sibling names must be unique |
| `form-folder-rename` | Rename. Sibling names must be unique |
| `form-folder-move` | Reparent and reorder. `next_sibling_id` `0` puts it last |
| `form-folder-assign` | Move forms into one folder. `folder_id` `0` unassigns them |
| `form-folder-delete` | Delete the folder and its subfolders. Forms are kept and unassigned |
| `form-create-json` | Create from JSON description |
| `form-get-json` | Full form JSON (fields, actions, conditionals) |
| `form-update-json` | Replace from JSON; keep structure; prefer granular field tools |
| `form-publish` | Draft → published. Fails when the form is locked |
| `form-draft` | Published → draft. Fails when the form is locked |
| `form-lock` | Block edits and trash until unlocked |
| `form-unlock` | Allow edits again. Use only when the user asks |
| `form-clone` | Duplicate (clone is draft) |
| `form-shortcode` | `[ws_form id="…"]` |
| `form-block` | Block editor markup only |
| `form-stats` | Views, saves, submits, unread, daily |
| `form-delete` | Trash or permanent (`permanent`). Locked forms cannot be trashed |
| `form-restore` | Restore from trash |

### Fields, tabs, sections

| Ability | Role |
|---|---|
| `fields` | List Field IDs, types, section/tab |
| `field-add` | Insert into `section_id` |
| `field-update` | Label/meta. Type cannot change |
| `field-delete` | Delete field |
| `tabs` | List tabs (groups) |
| `tab-add` | Add tab; add `tab_next` / `tab_previous` when multi-tab |
| `tab-update` | Tab label |
| `tab-delete` | Cannot delete last tab |
| `sections` | List sections (`group_id` = tab id) |
| `section-add` | Needs `group_id` |
| `section-update` | Label, `label_render`, and `widths` |
| `section-delete` | Cannot delete last section on a tab |

### Actions (PRO)

| Ability | Role |
|---|---|
| `actions` | List with stable `row_id` |
| `action-add` | `email`, `message`, or `redirect` |
| `action-update` | Prefer `row_id` |
| `action-delete` | Cannot delete last email, message, or database |

### Conditional Logic (PRO)

| Ability | Role |
|---|---|
| `conditionals` | List with `row_id` and IF/THEN summary |
| `conditional-add` | AI-shaped rule |
| `conditional-update` | Full rule replace; prefer `row_id` |
| `conditional-delete` | Prefer `row_id` |

### Submissions

| Ability | Role |
|---|---|
| `submissions` | List with filters |
| `submission-get` | One submission and field values |
| `submission-notes` | List notes |
| `submission-note-create` | Short `content`; structured `meta.values` / `meta.buttons` |
| `submission-note-update` | Same content rules |
| `submission-note-delete` | Delete note |

### Styles (Styler only)

| Ability | Role |
|---|---|
| `styles` | List |
| `style-get` | Palette |
| `style-create` | Only if user asked for visual appearance |
| `style-update` | Root palette keys only; `default` is site-wide. Fails when the style is locked |
| `style-lock` | Block edits and trash until unlocked |
| `style-unlock` | Allow edits again. Use only when the user asks |
| `style-delete` | Cannot delete the site default, the conversational default, or a locked style |

## Preferred read-before-write

1. List or get the object.
2. Use the returned IDs (`id`, Field ID, `section_id`, `group_id`, `row_id`).
3. Mutate with the narrowest Ability.
4. Re-get or use returned `url_preview` / JSON to verify.

Do not pass example IDs. Do not skip inspect on destructive tools (`form-delete`, `form-folder-delete`, `field-delete`, `tab-delete`, `section-delete`, `action-delete`, `conditional-delete`, `style-delete`, `submission-note-delete`).

Each form is in one folder. `folder_id` `0` means the form is not in a folder. Listing a parent folder returns only forms assigned to that folder. `form_count` is the same direct count. Knowledge base: [Form folders](https://wsform.com/knowledgebase/form-folders/).

[Form locking](https://wsform.com/knowledgebase/form-locking/) and [style locking](https://wsform.com/knowledgebase/style-locking/) block edits until unlock. List and get tools do not return a locked flag. A failed edit reports "This form is locked." or "This style is locked."

## Granular vs whole-form JSON

| Prefer granular | Use `form-update-json` |
|---|---|
| One field label/help/required/options | Coordinated edits that are not a single field/action/conditional and that the JSON tool is designed for (for example notification arrays, with the caveat that included `actions` / `conditionals` **replace** the current sets) |
| `field-add` / `field-delete` | Never to add/remove fields (keep structure) |
| `action-update` for one email | |

`form-update-json` description: prefer `field-update` for single-field changes; keep the same groups/sections/fields structure; Send Email lives in `actions`.

`form-create-json` description: email notifications are Send Email actions; do not create a style unless asked; if styling, create the form first, then `style-create`.

## Identifiers

- Form ID, tab ID, section ID, Field ID: numbers from list/get tools.
- Actions and conditionals: **`row_id` preferred** (stable). Optional `index` (1-based) or unique `label_match`.
- Conditional Logic that targets an action uses that action's `row_id`.
- Variables: `#field(123)` for Field ID 123.

## Safety

- MCP uses WordPress application passwords (or equivalent authenticated transport). Never embed those secrets in form JavaScript.
- Access is gated by WS Form capabilities (`read_form`, `edit_form`, `publish_form`, `delete_form`, `read_submission`, style caps, and others).
- Review AI-generated form changes before publishing to production. Official MCP documentation recommends staging.
- `form-create-json` and `field-add` accept only the type keys those tools list. Other native types cannot be created via Abilities. See [fields.md](fields.md).

## PHP extension (when Abilities are not enough)

Use documented WS Form hooks and filters. See the [Knowledge Base](https://wsform.com/knowledgebase/) developer and hook articles. Do not register unofficial MCP tool names and claim they are WS Form Abilities.
