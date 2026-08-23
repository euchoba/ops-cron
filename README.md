# ops-cron

Scheduled HTTP pings for background jobs belonging to the TurboTill and
OmniSolo apps.

This repo is intentionally **public** so its GitHub Actions runs are free
and unlimited, unlike the private `TurboTill` and `omnisolo` repos which
are subject to a monthly Actions minutes budget. These workflows do nothing
but `curl` a secret-protected endpoint on a schedule — no source code or
business logic lives here.

## Workflows

- `bunq-sync.yml` — hourly, calls OmniSolo's `POST /api/bank/sync`
  (matches incoming bunq payments against sent/overdue invoices).
- `turbotill-webhook-dispatch.yml` — hourly, calls TurboTill's
  `POST /api/webhooks/process` (delivers queued `webhook_events`, e.g.
  retail sales, to subscribers like OmniSolo).

## Secrets

Configured under repo Settings -> Secrets and variables -> Actions:

- `OMNISOLO_CRON_SECRET` - must match the `CRON_SECRET` env var set on the
  `omnisolo` Vercel project.
- `TURBOTILL_CRON_SECRET` - must match the `CRON_SECRET` env var set on the
  `turbo-till` Vercel project.

If either endpoint's `CRON_SECRET` is rotated in Vercel, update the
corresponding secret here too.

## Why not GitHub Actions in the private repos?

Private repos get a limited number of free Actions minutes per month, and
once exhausted, scheduled workflows just silently fail to start ("Actions
budget preventing further use"). Since these are just recurring HTTP pings
with no dependency on the app's source code, moving them to a public repo
sidesteps that limit entirely.
