# Third-Party Honorable Mentions

**Last verified: 2026-08-17**

Point services that aren't part of any single cloud's platform, but come up constantly in
free-tier architecture conversations because the "home" cloud doesn't offer a comparable
free tier for that specific job. Use these to fill a specific gap (see "gap" on each entry)
alongside an Azure-first (or other cloud) design — not as a replacement for the cloud
reference files.

Same rule as `azure.md`: these are numbers as of the verified date above, they change, and
consequential conversations should get a live web-search check before quoting them as current.

---

## Mailgun — transactional email

**Gap it fills:** Azure Communication Services Email has no perpetual free tier — it's
pay-as-you-go from the first message (see the Email section in `azure.md`). Mailgun's free
plan is a genuinely perpetual $0 tier, not a time-limited trial.

| Limit | Free plan |
|---|---|
| Sending volume | 100 emails/day |
| Sending domains | 1 custom domain |
| API keys | 2 |
| Log retention | 1 day |
| Support | Ticket only |
| Users | 1 per account |

**Gotchas:**
- 100/day (~3,000/month) is fine for transactional email on a small app (password resets,
  signup confirmations, low-volume notifications) — not enough for marketing sends or
  anything bursty. Flag this ceiling explicitly if the user describes anything beyond
  transactional volume.
- 1-day log retention on the free plan means delivery/debugging history disappears fast —
  worth mentioning if the user is building something where email deliverability matters
  and they'll want to investigate bounces later.
- Requires a verified custom sending domain (DNS records) to send from your own domain —
  not a blocker, just a setup step to budget time for, not a cost.
- Easy to integrate: REST API and SMTP relay both work with most frameworks' standard mail
  libraries (nodemailer, Django's email backend, etc.) with minimal glue code — this is
  part of why it's a good fit for a free-tier-conscious MVP, not just the price.

---

## Adding another entry

Keep the same shape as Mailgun above: a **"Gap it fills"** line naming the specific hole
in the cloud-native free tier, a scannable limits table, and a gotchas list. Only add a
service here if it's filling a real gap — a cloud-native free tier that's absent or clearly
worse for that specific job — not just "also has a free tier."
