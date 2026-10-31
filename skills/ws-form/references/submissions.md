# Submissions

WS Form already stores submissions. Do not create a custom submissions table or REST store when the Database action is appropriate.

Never query or update `wsf_submit` / `wsf_submit_meta` directly.

## Read submissions

| Task | Ability |
|---|---|
| List | `ws-form/submissions` |
| One record plus field values | `ws-form/submission-get` |
| Notes | `ws-form/submission-notes` |
| Add/update/delete notes | `ws-form/submission-note-create`, `submission-note-update`, `submission-note-delete` |
| Views / saves / submits | `ws-form/form-stats` |

`submissions` supports `date_from` / `date_to` (YYYY-MM-DD in the WordPress timezone), `status`, `keyword`, `limit`, `offset`, and ordering.

Status filter enum: `all` (excludes trash and spam), `publish` (submitted), `draft` (in progress), `error`, `spam`, `trash`.

## Statuses

| Code | Meaning |
|---|---|
| `draft` | In progress (Save & Continue) |
| `publish` | Submitted |
| `error` | Error |
| `spam` | Spam |
| `trash` | Trash |

Storage is the **Save Submission** action (`database`). Save & Continue uses the `save` button field plus that action. See [Save](https://wsform.com/knowledgebase/save/).

That action can auto-delete submissions after a number of days: move them to trash, or permanently delete them. Knowledge base: [Auto Delete Submissions](https://wsform.com/knowledgebase/auto-delete-submissions/).

CSV export in WP admin supports column selection and ordering. There is no export Ability. Use Abilities for read access. Do not scrape the database.

## Notes

Keep `content` as short note text. Put structured label/value rows in `meta.values` and action links in `meta.buttons`. For a system/third-party note, set `user_name` and optionally `locked` true.

## Statistics

`ws-form/form-stats` returns view/save/submit counts, unread counts, and optional daily figures for a date range. Use this before inventing analytics queries.

## Failed actions

Action results are stored on the submission (`actions` JSON, including per-action errors). The WordPress admin submission sidebar shows action errors.

`submission-get` currently returns field values; it may not include action error payloads. If a submit "did nothing" or an integration failed:

1. Confirm the form is published and the Database action exists.
2. Inspect the submission record in admin (status `error` vs `spam` vs `publish`).
3. Read action errors on that submission.
4. Enable the debug console if appropriate ([troubleshooting.md](troubleshooting.md)).
5. Do not replace the action with custom PHP until the failure is identified.

## Permissions

Submission Abilities use `read_submission` (and related caps). MCP does not bypass WordPress authentication.
