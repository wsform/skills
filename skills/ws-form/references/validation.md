# Validation

Use native WS Form validation. Do not ship a parallel client-only validator for fields WS Form already checks.

Client-side validation improves UX. It is not sufficient for security-sensitive requirements. Required, email, URL, telephone, captcha, files, dedupe, allow/deny, and keyword checks also run on the server for those features. Pattern and min/max length are primarily HTML5/client. Do not rely on those alone for security.

Knowledge base: [Form validation](https://wsform.com/knowledgebase/form-validation/).

## Native field validation

Ability-editable validation meta: `required` (`"on"` or empty), `invalid_feedback`, and `step` (number fields). Other validation settings exist on fields in the layout editor; do not invent them on `field-update`.

Use an Ability-allowed type so built-in rules apply: `email`, `url`, `tel`, `number`, and on PRO `file`. See [fields.md](fields.md).

The `validate` field type is available to Abilities on PRO and shows a consolidated validation summary. [Validation field](https://wsform.com/knowledgebase/validation/).

Conditional Logic can toggle `required` and `set_custom_validity`. That is still native validation, not custom JS.

## Server-side

On submit, WS Form validates at least:

- Required fields (honoring hidden/bypass flags posted for Conditional Logic)
- Email, URL, telephone sanitization/validation
- Captcha tokens
- File / signature / media uploads
- Dedupe
- Allow/deny lists
- Keyword blocklist on mappable fields
- Nonce

Hidden fields and Conditional Logic visibility are not a security boundary.

## Custom validation (official)

When native rules cannot express the requirement, use documented filters rather than a custom AJAX validator:

- [`wsf_submit_field_validate`](https://wsform.com/knowledgebase/wsf_submit_field_validate/): per field
- [`wsf_submit_validate`](https://wsform.com/knowledgebase/wsf_submit_validate/): whole form
- [`wsf_action_email_email_validate`](https://wsform.com/knowledgebase/wsf_action_email_email_validate/): email action recipients

Return formats are defined by those filters (string message, `false`, or action objects). Follow the Knowledge Base. Do not invent a new REST endpoint to "do validation properly".
