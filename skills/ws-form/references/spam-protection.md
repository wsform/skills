# Spam protection

Do not invent a custom anti-spam stack until native options are considered. Do not replace server-side protections with purely client-side checks.

Spam that meets the form **spam threshold** is stored with status `spam`. Other actions besides Save Submission are skipped.

## Native mechanisms

| Mechanism | How |
|---|---|
| Honeypot | Form setting `honeypot`. Server rejects a filled honeypot (HTTP 403) |
| reCAPTCHA | Native field type `recaptcha`. Cannot be created via Abilities. Add in the layout editor. [reCAPTCHA](https://wsform.com/knowledgebase/recaptcha/) |
| hCaptcha | Native field type `hcaptcha`. Cannot be created via Abilities. Add in the layout editor. [hCaptcha](https://wsform.com/knowledgebase/hcaptcha/) |
| Cloudflare Turnstile | Native field type `turnstile`. Cannot be created via Abilities. Add in the layout editor. [Turnstile](https://wsform.com/knowledgebase/turnstile/) |
| CaptchaFox | Native field type `captchafox`. Cannot be created via Abilities. Add in the layout editor. [CaptchaFox](https://wsform.com/knowledgebase/captchafox/) |
| Akismet | Form-level Akismet mapping when Akismet is available |
| AbuseIPDB | Optional IP reputation check |
| Keyword blocklist | Form settings `keyword_blocklist*` |
| IP throttling | Form settings `ip_limit*` |
| IP blocklist | Form settings `ip_blocklist*` |
| Spam threshold | Form setting `spam_threshold` (0–100, default 50). reCAPTCHA v3 contributes `spam_level` |

Do not claim other captcha providers. There is no generic "custom captcha" field type.

Site-wide API keys live in WS Form settings; field-level keys can override. Never put secret keys in frontend JavaScript.

## Agent workflow

1. If the user wants spam protection on a public form, enable honeypot (form setting). Captcha field types are native WS Form fields but **cannot** be created with `field-add` or `form-create-json`. Add them in the layout editor. Do not pass `recaptcha`, `hcaptcha`, `turnstile`, or `captchafox` to those Abilities.
2. For content spam, use keyword blocklist and/or Akismet rather than a custom regex plugin.
3. For abusive IPs, use IP throttle/blocklist or AbuseIPDB.
4. Inspect `spam` submissions before loosening rules.
5. Submit lock (`submit_lock`) prevents double clicks. It is not spam scoring.

Client widgets collect tokens. WS Form verifies those tokens on the server. A hidden checkbox in the theme is not a replacement.

Documented filters: [`wsf_submit_block_keywords`](https://wsform.com/knowledgebase/wsf_submit_block_keywords/), [`wsf_submit_block_ips`](https://wsform.com/knowledgebase/wsf_submit_block_ips/).
