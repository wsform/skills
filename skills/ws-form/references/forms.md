# Forms

WS Form models a form as:

**Form → groups (tabs) → sections → fields**

Groups are tabs. Multi-step forms are multiple tabs plus `tab_next` / `tab_previous` fields. Repeatable rows are **repeatable sections**, not a field type.

Each form, tab, section, and field has a numeric ID assigned by WS Form. Never invent IDs. Inspect first.

## Inspect before mutation

When connected to a site:

1. `ws-form/forms` to find the form (filter `published` if you only need embeddable forms; filter `folder_id` to list one folder, with `0` for forms in no folder).
2. For IDs only: `ws-form/tabs`, `ws-form/sections`, `ws-form/fields`.
3. For full configuration: `ws-form/form-get-json`.
4. For notifications and other actions: `ws-form/actions` (PRO).
5. For Conditional Logic: `ws-form/conditionals` (PRO).

Do not assume the current form matches a previous conversation. Re-read after another agent or person may have changed it.

## Creating forms

Use `ws-form/form-create-json` when building a new form from a description.

The JSON input schema is defined by the Ability itself (the `json` property description). Follow that schema. Do not invent a parallel format.

Practical rules:

- Structure is `groups[].sections[].fields[]`.
- Typically one tab (group) unless the user asked for a multi-step form.
- Do not include a Submit button in the fields array. WS Form adds it.
- Do not combine first and last name into one field.
- Email notifications are Send Email actions in `actions[]`, not form settings.
- Do not create a style unless the user asked for a color theme, branding, or visual appearance.
- New forms are **draft**. Call `ws-form/form-publish` before embedding on a live page.

Create JSON and `field-add` accept only the types those tools list (see [fields.md](fields.md)). Types outside that list are skipped or rejected. If the user needs a native type that is not in that list (signature, captcha, Google Address, and similar), add it in the layout editor. Do not pass those keys to Abilities, and do not fake them with custom inputs.

## Modifying forms

Prefer granular Abilities:

| Change | Ability |
|---|---|
| One field label or meta | `ws-form/field-update` |
| Add a field | `ws-form/field-add` |
| Delete a field | `ws-form/field-delete` |
| Tab label | `ws-form/tab-update` |
| Add/delete tab | `ws-form/tab-add` / `ws-form/tab-delete` |
| Section label, `label_render`, or `widths` | `ws-form/section-update` |
| Add/delete section | `ws-form/section-add` / `ws-form/section-delete` |
| One action | `ws-form/action-update` |
| One conditional | `ws-form/conditional-update` |

Use `ws-form/form-update-json` when the change is not a single-object edit (for example coordinated notification edits that the JSON update path is designed for). Start from `ws-form/form-get-json`. Keep the same groups/sections/fields structure. To add or remove fields, use `field-add` / `field-delete` instead of rewriting structure. When `actions` or `conditionals` are included, they replace the form's current actions or conditionals.

Do not rewrite an entire form to change a label.

## Form-level configuration

Common form-level concerns that already exist in WS Form (inspect the form JSON / admin settings rather than inventing custom systems):

- Published vs draft (`ws-form/form-publish`, `ws-form/form-draft`)
- Embedding: shortcode `[ws_form id="123"]` via `ws-form/form-shortcode`; block markup via `ws-form/form-block` (block editor only)
- Conversational forms (`conversational` meta, knowledge base: [Conversational Forms](https://wsform.com/knowledgebase/conversational-forms/))
- Per-form translation, including WPML, Polylang, and Weglot when those plugins are present ([Translate forms](https://wsform.com/knowledgebase/translate-forms/))
- Spam protection, keyword/IP limits (see [spam-protection.md](spam-protection.md))
- Framework (`framework` setting; default `ws-form`)
- Style assignment (`style_id`) when Styler is enabled
- Form folders (`folder_id`; `0` means the form is not in a folder)

Clone with `ws-form/form-clone` (clone is draft). Delete with `ws-form/form-delete` (trash by default; `permanent` true is irreversible). Restore with `ws-form/form-restore`. `form-delete` requires **Allow Deletes**.

## Form folders

Use `ws-form/form-folders` before creating, renaming, moving, or assigning. A form is in one folder. Listing or counting a parent folder covers only forms assigned directly to it. Folders cannot nest deeper than depth 5, and sibling names must be unique.

`ws-form/form-folder-delete` removes the folder and its subfolders. The forms remain and are no longer in a folder. Knowledge base: [Form folders](https://wsform.com/knowledgebase/form-folders/).

## Locking

`ws-form/form-lock` stops edits, publish, draft, and trash until `ws-form/form-unlock`. List and get tools do not return a locked flag. A rejected write says "This form is locked." Unlock only when the user asks. Knowledge base: [Form locking](https://wsform.com/knowledgebase/form-locking/).

## Identifiers

- Form ID, tab ID (group ID), section ID, and Field ID are numeric.
- Say **Field ID 123**, not Field 123.
- Action and conditional rows have a stable `row_id`. Prefer `row_id` over list position.
- Variable references use `#field(id)`, for example `#field(123)`.

Never hard-code IDs from examples into a live site.

## Embedding

After publish:

- Shortcode: `[ws_form id="123"]`
- Gutenberg: use `ws-form/form-block` markup, not the shortcode, on block-editor pages

Draft forms are not for live pages.
