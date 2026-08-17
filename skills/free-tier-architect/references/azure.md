# Azure Free Tier Reference

**Last verified: 2026-08-17** (live-checked against Microsoft Learn / Azure pricing pages
on this date — see Step 1 in `SKILL.md` before quoting these numbers in a consequential
conversation; re-verify if this date is more than a few months old).

This file is reference data, not prose to read verbatim to the user — pull the specific
line that's relevant, don't dump the whole file into the conversation.

---

## Azure Static Web Apps (Free plan)

| Limit | Free plan |
|---|---|
| Bandwidth | 100 GB / month per subscription |
| Custom domains | 2 per app |
| Staging (pre-production) environments | 3, auto-created per PR |
| Storage (prod + staging combined) | 500 MB total, 250 MB max per app |
| Managed Functions API | Included, subject to the Functions consumption limits below |
| SSL certificates | Free, auto-managed on default and custom domains |

**Gotchas:**
- Exceed 100 GB bandwidth → site stops being served until next billing cycle, not billed overage. Plan around this for anything with real traffic.
- 2 custom domains is per *app*, not per subscription — fine for most single-app projects, a constraint for multi-brand deployments off one codebase.
- Need >3 staging environments (e.g., long-lived feature branches) or >500 MB storage → Standard plan, which is paid.
- **A custom domain alone does not force you off Free** — that's a common misconception. What forces the move is needing App Service-level features (VNET integration, private endpoints, larger compute) that SWA doesn't offer at any tier.

---

## Cosmos DB free tier

| Limit | Free tier |
|---|---|
| Throughput | First 1000 RU/s free |
| Storage | First 25 GB free |
| Duration | Lifetime of the account (not a trial) |

**Gotchas (in order of how often they trip people up):**
- **Scope is the Cosmos DB *account*, not the database or container, and only one free-tier account is allowed per Azure subscription.** A second Cosmos DB account in the same subscription gets no free grant at all — full price from RU/s zero. If a project needs multiple logical databases, put them as multiple databases/containers *inside one free-tier account*, not as separate accounts.
- Usage beyond 1000 RU/s or 25 GB is billed at standard rate for the overage only — you won't see a line item for the free portion, just the excess. A bill showing a small nonzero Cosmos charge usually means you've crossed one of these, not that free tier stopped applying.
- If also using a brand-new Azure free account (first 12 months), that account's separate 400 RU/s / 25 GB credit stacks with the Cosmos free tier for a combined 1400 RU/s / 50 GB — but only for those 12 months. After that, it drops back to the standard 1000 RU/s / 25 GB Cosmos free tier, permanently.
- Occasionally the Azure Cost Management portal displays a confusing "Free 100 RU/s" style line — this is a display quirk in cost breakdown views, not an actual reduction; the account-level entitlement is still 1000 RU/s. Don't let this scare you (or a client) into thinking the grant shrank.
- Serverless capacity mode and free tier are mutually exclusive per account — you cannot enable free tier discount on a serverless Cosmos DB account. For an actual free-tier setup, use **provisioned throughput with autoscale disabled or capped**, not serverless, if you want the 1000 RU/s grant to apply. (If the user specifically wants serverless billing behavior instead of the free-tier grant, that's a legitimate choice — just make sure they know it's one or the other, not both.)

---

## Azure Functions — Consumption plan

| Limit | Free grant (per subscription, monthly) |
|---|---|
| Executions | 1,000,000 |
| Resource consumption | 400,000 GB-seconds |

**Gotchas:**
- The grant is **per subscription**, shared across every Function App in that subscription on the Consumption plan — not per-app. Multiple Function Apps in one subscription draw from the same pool.
- Every Function App requires a backing **Storage Account** (for triggers, logs, deployment package) — that storage account is billed normally and is *not* covered by the Functions free grant. It's usually pennies for a small app, but it's not zero, and it's easy to forget when someone says "Functions are free."
- Cold starts: Consumption plan instances scale to zero when idle, so the first request after idle time pays a startup latency cost (typically low-single-digit seconds, worse for larger deployment packages or .NET vs. Node/Python). Not a cost issue, but a UX one worth flagging for anything user-facing and latency-sensitive.
- SWA's **managed Functions integration** draws from this same Consumption-plan grant under the hood, but is simpler to deploy (bundled with the SWA app, single deployment) at the cost of fewer bindings and a narrower runtime version matrix. A **separate Function App** gives full binding/runtime flexibility and can be shared across multiple frontends, but is a second resource (and second storage account) to track — track it as its own line item, not as "part of the SWA free tier."

---

## Things with no meaningful perpetual free tier

Flag these immediately if they come up — they are the fastest way out of a free-tier design:

- **Any VM** (Azure VMs, App Service on anything above the free/shared tier) — free trial credit only, not a perpetual free tier.
- **Azure Cache for Redis** — no free perpetual tier; even the smallest (Basic C0) tier bills monthly.
- **Azure Service Bus** — Basic tier is low-cost but not free; only trial credit covers it temporarily.
- **App Service Plan** at Basic tier or above (needed once SWA's feature set is outgrown — VNET integration, always-on, larger compute) — paid from the first hour.
- **Durable Functions orchestration storage** — the orchestration logic runs under the normal Functions free grant, but the Storage Account backing orchestration state (queues, tables, blobs used for checkpointing) is billed separately, same as the base Storage Account gotcha above, just larger in practice because Durable Functions writes to it continuously.
- **Azure Communication Services — Email** — pay-as-you-go from the first message, no free
  grant at all: $0.00025/email sent + $0.00012/MB transferred. Cheap at low volume (~$1/mo
  for 100 emails/day) but not free. If the user just needs transactional email (signup
  confirmations, password resets) and free is the priority, point at Mailgun's free tier
  in `references/third-party.md` instead.

For each of these, when they come up: name the service, name what specifically triggers billing, and give a rough number — don't just say "that costs money."

---

## Common combos and their shape

**Simple CRUD web app (solo dev, low traffic):**
SWA (free) + SWA managed Functions (free, within Consumption grant) + Cosmos DB free-tier account (free, single account) → fits comfortably, $0 baseline.

**Same app + custom API needs (webhooks, background processing, non-JS runtime):**
SWA (free) + separate Function App on Consumption (free grant, own storage account — small nonzero cost) + Cosmos DB free-tier account (free) → still near-$0, now with two resources to watch instead of one.

**Same app + scheduled/long-running jobs:**
Add Durable Functions → same as above, plus the orchestration storage account cost grows with usage. Still cheap, no longer strictly $0.

**Multi-environment (dev + staging + prod) on one subscription:**
Only one Cosmos DB account gets the free-tier grant per subscription. Trying to give dev/staging/prod each their own Cosmos account means two of the three pay full price from RU/s zero. Either share one free-tier account across environments (with separate databases/containers) or accept that only one environment is actually free.
