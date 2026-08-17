---
description: Audit an architecture (described in chat, or the current repo) against Azure free-tier limits — what's free, what's paid and why, and a low-cost fix for anything paid.
---

# Free Tier Audit

Audit the architecture under discussion against Azure free-tier limits and report back
plainly. This command produces a point-in-time report; it doesn't change anything.

## What to audit

1. **If the user described an architecture in chat** (services, stack, rough shape), audit
   that description directly — don't demand a repo.
2. **If run inside a repo and no description was given**, do a light scan for signals of
   what's deployed — this is a lightweight signal scan, not IaC parsing:
   - `staticwebapp.config.json`, `swa-cli.config.json` → Static Web Apps
   - `host.json`, `function.json`, `local.settings.json`, a `functions/` or `api/`
     directory → Azure Functions, and whether it looks bundled with SWA or standalone
   - `*.bicep`, `main.bicep`, `*.tf`, `azuredeploy.json` → look for resource types
     declared (`Microsoft.DocumentDB/databaseAccounts`, `Microsoft.Web/sites`,
     `Microsoft.Cache/Redis`, `Microsoft.ServiceBus/namespaces`, VM resources, etc.)
   - `appsettings*.json`, `.env*` (names/keys only, never values) → connection strings or
     SDK references hinting at Cosmos DB, Redis, Service Bus, etc.
   - `azure-pipelines.yml`, `.github/workflows/*` → deployment targets referenced
   - If nothing in the repo looks Azure-related, say so and ask the user to describe the
     architecture instead rather than guessing.
3. If both a description and a repo are available, prefer what the user says over what the
   repo implies where they conflict, but flag the mismatch.

## Verification

Before quoting specific limits or dollar amounts, follow the same verification rule as the
skill: check `references/azure.md`'s "Last verified" date, and web search Microsoft
Learn / Azure pricing pages to confirm current figures if that date is stale or the
finding is going into something consequential (a client-facing report, a go/no-go
decision). Say which you did.

## Report format

For each service/resource identified, report three things — don't pad this with prose:

- **Status**: Free / Paid / Conditionally free (depends on usage)
- **Why**: the specific limit it's within, or the specific trigger that makes it paid
  (cite the relevant entry in `references/azure.md` rather than re-deriving the number)
- **If paid, the low-cost fix**: a concrete, specific alternative that would bring it back
  under free tier (e.g., "consolidate the second Cosmos DB account into the first — one
  free-tier account per subscription" or "swap the dedicated App Service plan for Static
  Web Apps if the custom-domain need is the only reason it's there")

Close with one line summarizing overall status: fully free / mostly free with N paid
items / not free-tier-shaped as designed — and if the last one, say briefly why (e.g., "this
needs a VM for X, which has no perpetual free tier — that's a scope call, not a
misconfiguration").

## Scope

This command reports and recommends; it does not generate Bicep/Terraform, edit files, or
deploy anything. If the user wants the fix actually implemented, that's a separate,
explicit ask.

For the underlying limits data, read `../skills/free-tier-architect/references/azure.md` —
don't duplicate its numbers here; this command's job is applying that data, not restating it.
