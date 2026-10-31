# Calculations

Use native **calculated fields** (`#calc()`) instead of custom JavaScript math.

There is no separate "calc" field type. Put `#calc(...)` in `default_value` on `price`, `number`, `text`, or `hidden`.

Knowledge base: [Calculated Fields](https://wsform.com/knowledgebase/calculated-fields/).

## How to implement

1. Use `price` for currency amounts.
2. Use `number` with `step` `any` for non-currency decimals.
3. Use `hidden` fields to break complex expressions into steps.
4. Keep at least one visible field that shows the result.
5. Inside `#calc()`, `#field(id)` is numeric. Do not expect strings. Map select/radio values to numbers if they feed a calculation.

Example:

```text
#calc(#field(10) * #field(11))
```

Field IDs must be real IDs from the form. Do not copy example IDs onto a live form.

If a variable contains double quotes, copy it exactly into `default_value` and escape those quotes in JSON. Example: `#query_var(\"utm_source\")`.

## Variables

Common variable families (client-side unless documented otherwise):

- `#calc(expression)`: JavaScript-style math
- `#field(id)`: field value
- `#text(...)`: dynamic text, not math ([copy field values](https://wsform.com/knowledgebase/duplicate-copy-field-values-with-text/))
- `#if(...) ... #endif`: conditional text ([if/endif](https://wsform.com/knowledgebase/if-endif/))
- Math helpers such as `#round()`, `#abs()`, `#min()`, `#max()`, `#ceil()`, `#floor()`
- Repeatable section helpers such as `#section_row_count(id)`
- E-commerce / price helpers when those fields are in use

Do not invent variable names. Use variables documented in the [Knowledge Base](https://wsform.com/knowledgebase/).

`#calc()` is evaluated in the browser. The server stores submitted values; it does not re-run `#calc()` as a security control. If a computed value must be trusted, validate or recompute server-side with a documented hook.

## E-commerce vs `#calc()`

WS Form has quantity, price-select, and cart field types, but Abilities cannot create them. Abilities can create `price` (PRO) plus `number` / `text` / `hidden` and put `#calc()` in `default_value`. Do not pass `quantity`, `price_select`, or `cart_total` to `field-add`.

If the user needs the full cart engine, configure those fields in the layout editor. See [introduction to e-commerce](https://wsform.com/knowledgebase/introduction-e-commerce/).

## Caveats

- PRO feature. Do not replace it with custom JavaScript on LITE unless the user explicitly requires that.
- All `#calc()` operands must be numeric.
- Prefer Ability field-update / create JSON `default_value` over injecting script tags.
- Repeatable sections need the section variables, not ad-hoc index math in custom JS.
