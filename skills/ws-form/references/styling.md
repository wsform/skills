# Styling

Prefer supported WS Form styling over fragile DOM manipulation or one-off CSS that targets implementation accidents.

## Styler vs Customizer

When the framework setting is `ws-form` and Styler is enabled:

- Use **Styler** (styles with palettes compiled to CSS variables)
- Form meta `style_id` assigns a style
- Abilities: `ws-form/styles`, `style-get`, `style-create`, `style-update`, `style-delete`

When the framework is Bootstrap or Foundation (or Styler is off):

- Styler abilities are not registered
- Skin/Customizer paths apply instead
- Do not call `style-create`

**Do not create a style unless the user specifically asks for a color theme, branding, or visual appearance.** Ordinary form creation should use the default style. If they do want a theme, create the form first, then `style-create` with `form_id` and root palette colors.

`style-create` / `style-update` accept root palette keys only (form colors, fonts, border radius, grid gap, and matching `_alt` keys). Setting `default` is site-wide. Default styles cannot be deleted.

Conversational forms can use a separate default (`default_conv` / `style_id_conv`). See [Conversational Forms](https://wsform.com/knowledgebase/conversational-forms/).

## CSS variables

Styler compiles palette settings to custom properties such as `--wsf-form-color-primary`, `--wsf-form-font-size`, `--wsf-form-border-radius`, `--wsf-form-grid-gap`, and field-level `--wsf-field-*` variables.

Prefer overriding those variables, scoped to the form, instead of restyling every input by tag name.

## Stable classes (ws-form framework)

Target documented classes, not generated internals:

| Class | Role |
|---|---|
| `wsf-form` | Form root (`data-wsf-style-id` when styled) |
| `wsf-group`, `wsf-group-tabs` | Tabs |
| `wsf-section` | Section |
| `wsf-field-wrapper` | Field container |
| `wsf-field` | Control |
| `wsf-label` | Label |
| `wsf-help` | Help text |
| `wsf-invalid-feedback` | Validation message |
| `wsf-button-primary` (and related button classes) | Buttons |
| `wsf-grid`, `wsf-tile` | Grid |

Field/section `class` meta is additive. Use it for extra classes rather than wrapping the form in custom markup.

## Custom CSS

If code is required:

1. Scope to `.wsf-form` (and `data-wsf-style-id` when present).
2. Prefer CSS variables.
3. Do not query-select by auto-generated IDs that are not Field IDs you were given.
4. Do not disable Styler CSS and replace it with a full custom stylesheet unless the user asked for that.

Framework-specific column classes apply when Bootstrap or Foundation is the active framework. Do not mix those with Styler assumptions.
