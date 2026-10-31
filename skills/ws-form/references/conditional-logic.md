# Conditional Logic

Use native **Conditional Logic** for show/hide, required/optional, enable/disable, set value, and related form behavior.

Do not add custom JavaScript merely to show a field when another field equals a value.

Product name: **Conditional Logic**. Knowledge base: [Conditional Logic](https://wsform.com/knowledgebase/conditional-logic/).

## When to use it

Use Conditional Logic when the user wants:

- If Field ID 123 equals Healthcare, show a locations field
- Hide a section until a checkbox is checked
- Make a field required only in some cases
- Enable or disable fields
- Run or skip an action on submit
- React to logged-in state, user role, or tab/section validity

## Inspect first

1. `ws-form/conditionals` (PRO) returns `row_id`, `index`, `label`, and a short IF/THEN summary.
2. Prefer `row_id` for `conditional-update` and `conditional-delete`.
3. Use `ws-form/form-get-json` if you need the full rule.

Do not assume row order. `row_id` survives reorder. `index` is 1-based from the list tool. `label_match` fails if the label is not unique.

Action targets in Conditional Logic use the action's `row_id` from `ws-form/actions`.

## Ability shape

`conditional-add` and `conditional-update` use the same AI shape as `form-create-json` conditionals. Follow the Ability input description. A typical rule:

```json
{
	"label": "Show locations for healthcare",
	"if": [{"field": 123, "logic": "equals", "value": "healthcare"}],
	"then": [{"section": 2, "do": "show"}],
	"else": [{"section": 2, "do": "hide"}]
}
```

Put the controlling field in an earlier section than the fields it reveals.

Ability IF entries target fields only. Multiple `if` entries in one rule are combined with AND. Separate conditionals are separate rules.

Allowed IF `logic` values for `conditional-add` and `conditional-update`: `equals`, `equals_not`, `blank`, `blank_not`, `contains`, `contains_not`, `gt`, `lt`, `gte`, `lte`, `checked`, `checked_not`.

Allowed `do` values:

| Target | `do` |
|---|---|
| Section | `show`, `hide` |
| Field | `show`, `hide`, `require`, `optional`, `enable`, `disable`, `set_value` |
| Action | `run_on_submit`, `do_not_run_on_submit` |

`else` is optional. When it is omitted, WS Form inverts the matching pair (`show`/`hide`, `require`/`optional`, `enable`/`disable`, `run_on_submit`/`do_not_run_on_submit`).

On `form-create-json`, an action target is the 1-based position in the final actions array, and that position becomes the `row_id`. Final order is email actions, then message actions, then the database action, then redirect actions. On an existing form, use the action `row_id`.

The layout editor supports a wider set of conditions and actions than this Ability shape. Use the live Ability description for Ability calls. Use the layout editor for behavior the Ability shape does not list.

## What native Conditional Logic covers

The product supports conditions and then/else actions on **form**, **tab**, **section**, **field**, **action**, **submit**, **user**, and **variable** objects.

Condition categories include:

- Field values (equals, contains, blank, regex, numeric comparisons, selected/checked, dates, files, character/word counts)
- Validity (including captcha field validity)
- Events (click, change, input, form submit/save events, and others documented for Conditional Logic)
- Repeatable section row counts
- User logged in, role, and capability
- Tab active state

Then/else actions include visibility, required, disabled, readonly, set value, classes, form submit/save/reset, action run flags, and many field-specific setters.

Do not invent condition or action keys. If a behavior is not in the live Ability description or [Conditional Logic](https://wsform.com/knowledgebase/conditional-logic/) documentation, do not claim it.

## Client vs server

Conditional Logic is evaluated in the browser. The server honors posted hidden/bypass state for required fields and still applies restrictions such as user role at submit.

Do not treat Conditional Logic as a security boundary. For security-sensitive rules, use server-side validation or documented PHP filters. See [validation.md](validation.md).

## When custom JavaScript might be justified

- Third-party widgets with no native Conditional Logic action
- Side effects that are not visibility, values, required, disabled, or action-run flags

WS Form also has a Conditional Logic `javascript` action (requires `unfiltered_html`) and a Run JavaScript **form action**. Neither is a substitute for show/hide.

Do not duplicate `#calc()`, `#text()`, or `#if()` with custom JS. See [calculations.md](calculations.md).
