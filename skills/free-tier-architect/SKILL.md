---
name: free-tier-architect
description: >
  Use this skill whenever the user is starting a new project, discussing application
  architecture, choosing a database or hosting platform, sketching a system design, or
  asking anything like "how should I build/host/deploy/structure this." Proactively steer
  the design toward services that fit within cloud free tiers (Azure-first: Static Web Apps
  + Cosmos DB serverless + Functions consumption plan) and speak up the moment a choice
  would require a paid tier — explain what triggers the cost and roughly how much before
  the user commits to it. Trigger even if the user has not said "free tier," "budget," or
  "cost" out loud — assume cost sensitivity is relevant to any early-stage architecture or
  hosting conversation unless the user has explicitly said budget isn't a concern. Applies
  equally to a hobbyist prototyping a side project and a consultant scoping a client MVP.
---

# Free Tier Architect

Help the user design applications that can be built, deployed, and run entirely within
cloud free-tier limits — and tell them the moment a decision would step outside that,
before they've built on top of it.

This is an advisory skill. It steers conversation and decisions; it does not scaffold
repos or generate IaC (Bicep/Terraform) in this version. See `/free-tier-audit` for a
point-in-time audit of an architecture already in progress.

## Who this is for

The same guidance serves two audiences without a mode switch:

- A **hobbyist/solo developer** prototyping a side project who doesn't want a surprise bill.
- A **consultant** scoping an MVP for a client who wants to know the real ceiling before quoting it.

Don't ask which one the user is. The advice is identical either way — only whose money is
on the line differs, and that doesn't change what fits in a free tier.

## Cloud scope

Azure-first for now. Detailed, current limits live in `references/azure.md` — read it in
whenever the conversation gets specific about services or numbers. Stubs exist at
`references/aws.md`, `references/gcp.md`, and `references/vercel-supabase.md` for other
platforms; if the user is targeting one of those, say plainly that this skill's coverage
there is thin today and give best-effort guidance rather than pretending to depth Azure has.

## Step 1: Verify before quoting numbers

Free tier limits and pricing change over time, and being wrong here is worse than being
vague. Before you state a specific number (RU/s, GB, execution count, bandwidth cap,
dollar amount) in a conversation where the user is making a real decision:

1. Check `references/azure.md` for the baked-in figure and its "Last verified" date.
2. If that date is more than a few months old, or the decision is consequential (the user
   is about to commit to an architecture, or asking "will this stay free"), **web search**
   official Microsoft Learn / Azure pricing pages to confirm the number is still current
   before quoting it.
3. Tell the user which you did: "per Azure's pricing page as of today" vs. "per my notes,
   last verified <date> — worth double-checking if this is going into a client proposal."

Never present a stale baked-in number as if it were just confirmed. When a live search
isn't practical (offline, low-stakes exploratory chat), say you're working from the
baked-in figures and give the verified-as-of date.

## Step 2: Walk the decision framework

When someone describes an app idea, work through this in order. Don't dump all of it at
once — surface each piece as it becomes relevant to what they're describing.

**Frontend** → Azure Static Web Apps (free tier). Ask what it needs to reach beyond static
hosting (auth, API, custom domain) before recommending anything heavier.

**Data** → Cosmos DB serverless free tier. The single biggest gotcha: **the 1000 RU/s +
25GB free grant is per Azure subscription, not per database or per container.** If the
user already has another Cosmos DB account in the same subscription, or is about to spin
up a separate one for a second project, they've likely already spent (or are about to
split) that grant. Flag this explicitly — it's the most common way people accidentally
leave free tier without noticing.

**API** → Two paths, both free-tier-eligible, different tradeoffs:
- SWA's built-in **managed Functions**: simplest, deploys with the frontend, but limited
  (Node runtime restrictions, fewer bindings, tied to the SWA app's lifecycle).
- A **separate Function App** on the Consumption plan: more control, more binding types,
  can be reused across frontends — but it's a *second* free-tier grant to track
  independently (its own monthly execution/GB-seconds allowance, separate from SWA's).

Point the user at `references/azure.md` for the current numbers behind each of these
before they lock in a choice.

## Step 3: Watch for "you're about to leave free tier"

Treat these as trigger conditions during the conversation — the moment one comes up, stop
and name it, explain what specifically causes the cost, and give a rough number (verified
per Step 1 if the stakes warrant it):

- A **second Cosmos DB account** in the same subscription (splits the shared free grant).
- A **custom domain that needs App Service** instead of SWA (SWA supports custom domains
  free; App Service does not have a comparable perpetual free tier).
- **Background/scheduled jobs via Durable Functions** — the orchestration itself may stay
  in the Functions free grant, but the storage account backing it (queues, tables, blobs
  for orchestration state) bills separately and isn't itself free-tier-covered.
- Anything needing a **VM** (no meaningful perpetual free tier beyond a short trial).
- **Azure Cache for Redis** — no perpetual free tier; even the smallest tier bills monthly.
- **Azure Service Bus** — the Basic tier is cheap but not free; only the free trial credit
  covers it temporarily.
- **Multiple environments** (dev/staging/prod) multiplying the above — SWA gives free
  staging environments tied to PRs, but a fully separate prod-like Cosmos/Functions setup
  for staging does not get a second free grant.

The pattern to avoid: naming that something costs money without saying what triggers it or
how much. Always give both.

## Step 4: Say where this stops working

Free-tier architecture is a starting point, not a forever plan. Note this when it becomes
relevant rather than as a disclaimer nobody reads:

- **Real production traffic** will outgrow Cosmos DB's 1000 RU/s and Functions' monthly
  execution grant — that's normal, not a design failure, but the user should know roughly
  what tier comes next and what it costs.
- **Multiple environments** (dev/test/staging/prod) strain a single-subscription free grant
  fast, since most Azure free tiers are subscription-wide, not per-environment.
- **Team collaboration** (multiple developers, CI/CD running frequently, shared resources)
  tends to consume free-tier allowances faster than solo development.

Frame this as "here's when to revisit," not "free tier won't work" — the goal is to get
them shipped cheaply now with eyes open about the next step.

## Tone

Be concrete, not alarmist. "That's fine on free tier" is as important a thing to say as
"that'll cost you" — don't manufacture caution where none is needed. When something is
free, say so plainly and move on.
