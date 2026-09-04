# Troubleshooting

Identify the failure before changing configuration. Do not speculatively rewrite the form or replace submit with custom PHP.

## Diagnostic sequence

1. Inspect the form (`ws-form/form-get-json` or the layout editor). Confirm it is **published** if it should appear on a live page.
2. Inspect the affected field, action, or Conditional Logic row by ID / `row_id`.
3. Inspect submissions (`ws-form/submissions`, `ws-form/submission-get`) for status `error`, `spam`, `draft`, or `publish`.
4. Inspect action errors on the submission in WP admin (per-action errors are stored on the submission).
5. Inspect the debug console when enabled.
6. Inspect PHP / server logs if the request never reaches WS Form.
7. Only then change configuration.

## Published vs draft

Unpublished forms cannot be used on live pages. `ws-form/form-create-json` and `ws-form/form-clone` produce drafts. Publish with `ws-form/form-publish`. `ws-form/form-draft` takes a live form offline.

Preview URLs returned by Abilities (`url_preview`) are for checking a form, not a substitute for publish.

## Debug console

PRO setting Debug Console: Off, Administrator (default), or On.

When enabled, the public debug console logs events and action errors. Knowledge base: [Debug Console](https://wsform.com/knowledgebase/debug-console/).

Do not enable On for everyone on production without the user asking.

`WP_DEBUG` is independent WordPress logging. Check it for PHP fatals, but form-level action failures often appear in the debug console or submission action errors first.

## Common submit failures

| Symptom | Check |
|---|---|
| Nothing stored | Database action present? Form published? JS errors? |
| Stored as spam | Captcha, Akismet, spam threshold, keyword/IP rules |
| Stored as error | Validation, required hidden fields, action errors |
| Email missing | Send Email action exists? `meta.to` correct? `#field(id)` points at the email Field ID? |
| Integration did not fire | Action `row_id` not skipped by Conditional Logic? Add-on installed? Spam skip? |
| Captcha fails | Keys in WS Form settings; server-side verify, not only the widget |
| Files missing | `file` field is PRO; handler and size limits |

## Abilities / MCP "cannot update"

Write tools require **Allow Updates** (WS Form → Settings → AI, default off). Deletes require **Allow Deletes**. If list/get tools work but update tools fail or are absent, check those settings.

MCP also requires the MCP Adapter and an authenticated WordPress user (application password). See [abilities-and-mcp.md](abilities-and-mcp.md).

## What not to do

- Do not drop the Database action to "fix" submit.
- Do not bypass nonce or captcha from custom JS.
- Do not edit `wsf_*` tables.
- Do not replace Conditional Logic with jQuery show/hide while debugging unless you have already proved Conditional Logic is the failure.
