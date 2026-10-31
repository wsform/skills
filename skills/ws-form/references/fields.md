# Fields

`ws-form/form-create-json` and `ws-form/field-add` accept **only** the type keys listed by those tools (the `field-add` `type` enum).

Unknown types are skipped in create JSON and rejected by `field-add` (`Invalid field type`). Do not pass type keys that are not in that list.

The live tool description is authoritative if it differs from this file.

Refer to identifiers as **Field ID 123**, not Field 123.

Field **type cannot be changed** after creation.

Do not include a `submit` field. WS Form adds the submit button. `submit` is not in the Ability type list.

## Types you can create via Abilities

Use these instead of a generic `text` field when they match the need.

### Always (LITE and PRO)

| Need | Type key | Notes |
|---|---|---|
| Short text | `text` | Default single-line input |
| Long text | `textarea` | |
| Email | `email` | Native email validation |
| Phone | `tel` | |
| Website | `url` | |
| Number | `number` | `step` `any` or `0.01` for decimals |
| Dropdown | `select` | Options via `meta.options` |
| Checkboxes | `checkbox` | Options via `meta.options` |
| Radios | `radio` | Options via `meta.options` |
| Instructions for the user | `texteditor` | Visible content in `meta.text_editor` |
| Admin-only layout note | `note` | `meta.text_editor`; not shown on the public form |

### PRO only

Available when using WS Form PRO. Not available when creating fields against WS Form LITE.

| Need | Type key | Notes |
|---|---|---|
| Date and/or time | `datetime` | |
| File upload | `file` | When FileBird, HappyFiles, or Premio Folders is active, choose its folder in the layout editor |
| Hidden value | `hidden` | Only `default_value` is meaningful for AI |
| Color | `color` | |
| Rating | `rating` | 0 to 5 |
| Price / amount | `price` | Currency-style amounts; also used with `#calc()` |
| Validation summary | `validate` | Consolidated errors on the form |
| Next tab | `tab_next` | Label `Next`. No meta. Not on the last tab |
| Previous tab | `tab_previous` | Label `Previous`. No meta. Not on the first tab |

If both `tab_next` and `tab_previous` are on the same tab, set create-only `width_factor` to `0.5`.

Knowledge base examples: [Text](https://wsform.com/knowledgebase/text/), [Email](https://wsform.com/knowledgebase/email/), [Phone](https://wsform.com/knowledgebase/tel/), [Select](https://wsform.com/knowledgebase/select/), [File Upload](https://wsform.com/knowledgebase/file/), [Date/Time](https://wsform.com/knowledgebase/datetime/), [Price](https://wsform.com/knowledgebase/price/).

## Do not pass these to form-create-json or field-add

WS Form has many other native field types. Abilities cannot create them. Do not invent custom HTML/JS for them either. Add them in the WS Form layout editor, or leave existing fields of those types in place.

Examples of native types that current Abilities cannot create:

- Signature, media capture (`mediacapture`), password, HTML, divider, spacer, summary, legal, range, progress, search, meter, SSN
- `message` field (on-form content). The Show Message confirmation is an action, not this field
- Google Address, Google Map, Google Route
- reCAPTCHA, hCaptcha, Turnstile, CaptchaFox, TrustedForm
- Save & Continue (`save`), reset, clear, generic `button`
- Repeatable section controls (`section_add`, `section_delete`, `section_up`, `section_down`, `section_icons`)
- E-commerce beyond `price` (`quantity`, `price_select`, `price_checkbox`, `price_radio`, `price_range`, `price_subtotal`, `cart_price`, `cart_total`)
- Submit (`submit`)

Google Address and Google Route use WS Form → Settings → Advanced. For Google accounts registered after March 1, 2025, choose Places API (New) and Routes API (New).

Full product index: [Field types](https://wsform.com/knowledgebase_category/field-types/).

If a form already contains a type outside the Ability list, do not delete and recreate it as `text`. Inspect it. You may update label/editable meta with `field-update` where that Ability allows. You cannot change its type.

## Ability meta

These are the meta keys `field-add` and `field-update` accept. Do not invent keys. Follow the live tool schema if it differs.

Editable via create and `field-update`:

| Meta | Purpose |
|---|---|
| `required` | `"on"` or empty |
| `placeholder` | |
| `help` | Help under the field |
| `text_editor` | `texteditor` and `note` content |
| `default_value` | Defaults and `#calc()` / `#text()` |
| `step` | Number fields |
| `invalid_feedback` | Validation message |
| `options` | `select`, `checkbox`, `radio` only |
| `widths` | Fraction of the row per breakpoint, greater than 0 and at most 1. See [sections-and-layout.md](sections-and-layout.md) |

Create-only (not `field-update`):

| Meta | Purpose |
|---|---|
| `width_factor` | `0.5` only. Half width at every screen size. Omit for full width |

Use `widths` when the width should change by screen size. `#calc(...)` in `default_value` is allowed on `price`, `number`, `text`, and `hidden` only. See [calculations.md](calculations.md).

If a variable in `default_value` contains double quotes, copy it exactly and escape the quotes inside the JSON string. Example: `#query_var(\"utm_source\")`.

## Options

For `select`, `checkbox`, and `radio`, pass:

```json
[{"value":"healthcare","label":"Healthcare"},{"value":"other","label":"Other"}]
```

Numeric `value` keys if the field feeds `#calc()`.

## Changing an existing field

1. Resolve the Field ID with `ws-form/fields` or `ws-form/form-get-json`.
2. Prefer `ws-form/field-update` for label and editable meta.
3. Do not change `type`.
4. To add a field, use `field-add` with a type from the list above and a real `section_id`. Optional `field_id_before` inserts before another field.
5. Use the returned Field ID. Do not guess.
