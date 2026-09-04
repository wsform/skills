# Actions and integrations

If WS Form provides a native action or official add-on for the requested integration, use that instead of implementing the external API directly unless the user requires unsupported behavior.

Inspect installed actions before writing HTTP clients, CRON jobs, or custom tables.

## Inspect first

1. `ws-form/actions` (PRO) returns `row_id`, `index`, type `id`, `label`, and `meta_summary`.
2. Prefer `row_id` for `action-update` and `action-delete`.
3. `ws-form/form-get-json` includes the actions array.

Email notifications are **Send Email** actions (`id`: `email`), not form settings.

## Core action type keys

These ship in the WS Form plugin (edition-dependent):

| Type key | Label | Use for |
|---|---|---|
| `email` | Send Email | Notifications. `meta.to` may be a literal address or `#field(id)` for the person completing the form |
| `message` | Show Message | On-screen confirmation |
| `database` | Save Submission | Store the submission. Required. Do not replace with a custom table |
| `redirect` | Redirect | After submit |
| `customendpoint` | Webhook | HTTP request to an arbitrary endpoint (PRO) |
| `hook` | Run WordPress Hook | `do_action` / `apply_filters` with `$form`, `$submit` |
| `javascript` | Run JavaScript | Browser JS after submit (PRO) |
| `conversion` | Conversion Tracking | Analytics pixels / dataLayer (PRO) |
| `search` | Run WordPress Search | WP search |
| `data_export_request` | Data Export Request | GDPR export |
| `data_erasure_request` | Data Erasure Request | GDPR erasure |
| `wp_ai_client` | Make AI Request | WordPress AI Client, when that client is available |

`ws-form/action-add` currently accepts only `email`, `message`, and `redirect`. Other action types are configured in the form (or via JSON update where supported), not invented as new Ability names.

Do not delete the last `email`, `message`, or `database` action.

New forms should include at least one Send Email, one Show Message, and exactly one Save Submission action.

## Webhook vs hook vs JavaScript

| Need | Use |
|---|---|
| Call an external HTTP API | Official add-on if one exists; otherwise **Webhook** (`customendpoint`) |
| Run PHP inside WordPress | **Run WordPress Hook** (`hook`) |
| Browser-only tracking or DOM work after submit | **Run JavaScript** or **Conversion Tracking** |

[Webhook](https://wsform.com/knowledgebase/webhook/) is the generic HTTP action. It is not a reason to skip an official CRM/payment add-on that already maps fields.

## Official add-ons

CRM, payment, email-marketing, and automation products are **separate plugins**, not built-in WS Form actions. Examples from WS Form documentation include Salesforce, Mailchimp, HubSpot, Stripe Elements, Zapier, Google Sheets, WooCommerce, and others listed under [Integrations](https://wsform.com/knowledgebase/).

For "send these leads to Salesforce":

1. Determine whether the official [Salesforce integration](https://wsform.com/knowledgebase/salesforce/) is installed. Check WS Form settings (Integrations) and whether an Add to Salesforce action appears on the form. Official add-ons are not compatible with WS Form LITE.
2. If it is installed, configure that action. Do not write a custom Salesforce REST client.
3. `ws-form/action-add` cannot create this action (it only adds `email`, `message`, or `redirect`). Configure it in the form Actions sidebar.
4. If the add-on is not installed, recommend it. Use Webhook only when the user needs an unsupported endpoint and accepts generic mapping and auth.

Do not claim an add-on is installed because this skill mentioned it. Check the site.

Discover installed actions at runtime:

- Ability `ws-form/actions` on a form
- Active plugins (official add-ons are separate WS Form plugins)
- The form Actions sidebar in the layout editor

## Events

Actions run on form events stored per row (`events`). Typical values:

- `submit`: form submitted
- `save`: form saved (Save & Continue; database action includes both `save` and `submit`)

Conditional Logic can set `run_on_submit` / `do_not_run_on_submit` using the action `row_id`.

Spam can skip all actions except Save Submission. See [spam-protection.md](spam-protection.md).

## Custom code

When no action or add-on exists, use documented WS Form hooks and the Run WordPress Hook action. See the [Knowledge Base](https://wsform.com/knowledgebase/) for developers.

Do not create a parallel submission pipeline.
