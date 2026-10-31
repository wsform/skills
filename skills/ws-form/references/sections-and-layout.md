# Sections and layout

Do not implement a custom layout system. WS Form already has tabs, sections, a responsive grid, and repeatable sections.

## Architecture

```
Form
 └── group (UI: Tab)
      └── section
           └── fields
```

In form JSON and Abilities, tabs are **groups** (`group_id`). Abilities use the word **tab** and accept `group_id` as the tab ID (`ws-form/tabs`, `tab-add`, `section-add`).

Sections group fields. Fields are not placed on the form independently of a section.

## Tabs (multi-step)

Multiple groups are multiple tabs. Use this for multi-step forms.

- List: `ws-form/tabs`
- Add: `ws-form/tab-add` (optional `tab_id_before`)
- Update label: `ws-form/tab-update`
- Delete: `ws-form/tab-delete` (cannot delete the last tab)

When a form has more than one tab, add `tab_next` and `tab_previous` with `field-add` **only on PRO** (those types are in the PRO Ability type list). Put them at the bottom of the tab. Never put Previous on the first tab or Next on the last tab. If both appear on the same tab, set `width_factor` to `0.5`. Do not pass `submit`, `save`, or other button types to `field-add`.

Related form settings (inspect JSON; do not reinvent):

- `tab_validation` / `tab_validation_show`: require the current tab to validate before advancing
- `tabs_hide`: hide tab navigation while keeping tab behavior
- `cookie_tab_index`: remember last tab

## Sections

- List: `ws-form/sections` (includes parent `group_id`)
- Add: `ws-form/section-add` (requires `group_id` from `tabs`)
- Update: `ws-form/section-update` (label, `label_render`, and `widths`)
- Delete: `ws-form/section-delete` (cannot delete the last section on a tab; deletes the section's fields)

Show the section heading with `label_render` `"on"`. Default is hidden.

## Grid and width

The default WS Form framework uses a 12-column grid (`framework_column_count`, default 12). Breakpoints come from the active framework.

`width_factor` is create-only and may only be `0.5`. It sets the smallest breakpoint, so that half width applies at every screen size. Omit it for full width.

`widths` is the meta for any other width, including a different width per breakpoint. Each value is a fraction of the row, greater than 0 and at most 1. `1` is full width. Omit a breakpoint to inherit the previous one. On update, omit a breakpoint to leave it unchanged. Set a breakpoint to `null` or `""` to clear it so it inherits again.

On the default `ws-form` framework the keys are `25` (extra small, default, up to 575px), `50` (small), `75` (medium), `100` (large, from 992px), and `150` (extra large, from 1200px). A later breakpoint that is omitted inherits the previous value. Example: `{"25":1,"100":0.5}` is full width below 992px and half width from 992px up. Other frameworks use their own keys. Follow the live tool description.

Section `widths` can be set on create JSON or with `section-update`. Field `widths` can be set on create JSON, `field-add`, or `field-update`.

Form meta `breakpoint_query` chooses whether those breakpoints follow browser width (`media`, the default) or form width (`container`). Form width suits sidebars and modals. Knowledge base: [Breakpoint queries](https://wsform.com/knowledgebase/breakpoint-queries/).

Prefer native width settings over a custom CSS grid.

## Repeatable sections

WS Form does **not** use a "repeater field". Repeating rows are a **repeatable section**.

Enable with section meta `section_repeatable` `"on"` (PRO). Related settings include `section_repeat_min`, `section_repeat_max`, `section_repeat_default`, and delete confirmation.

Row controls are native field types (`section_icons`, `section_add`, `section_delete`, `section_up`, `section_down`). `field-add` cannot create them.

For a quote form with a repeating product list: one repeatable section containing the product fields, not custom JavaScript that clones DOM nodes.

Variables such as `#section_row_count(id)`, `#section_row_number`, `#section_row_index`, and `#section_loop_start(id)` / `#section_loop_end(id)` exist for output and calculations. See [Repeatable Sections](https://wsform.com/knowledgebase/repeatable-sections/).

Abilities cannot currently enable `section_repeatable` or add section-control fields. Configure repeatable sections in the layout editor. Do not pass `section_add` or related type keys to `field-add`.

## Frameworks

Setting key: `framework`. Default: `ws-form`.

Also supported: Bootstrap 3.x, 4.0, 4.1-4.6, and 5+, and Foundation 5.x, 6.0-6.3.1, and 6.4+. WS Form outputs framework-native markup when those are selected.

**Styler only works when `framework` is `ws-form`.** See [styling.md](styling.md).

Do not wrap the form in a custom grid framework to "fix" layout.

## Agent workflow

1. Inspect tabs and sections before adding fields.
2. Add a tab only if the user needs another step.
3. Add a section when fields belong in a distinct group (or a repeatable row).
4. Add fields into an existing `section_id`.
5. For multi-step on PRO, add `tab_next` / `tab_previous` after the tabs exist.
