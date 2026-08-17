<div align="center">

<img src="assets/header.svg" alt="free-tier: it doesn't need much to survive" />

[![Validate plugin](https://github.com/brandonmagaddino/free-tier/actions/workflows/validate.yml/badge.svg)](.github/workflows/validate.yml)
[![License](https://img.shields.io/github/license/brandonmagaddino/free-tier)](LICENSE)
[![Works with Claude Code](https://img.shields.io/badge/works%20with-Claude%20Code-5B8C55)](https://claude.com/claude-code)
[![Latest release](https://img.shields.io/github/v/release/brandonmagaddino/free-tier)](https://github.com/brandonmagaddino/free-tier/releases)

</div>

---

A Claude Code plugin that architects new applications to be built, deployed, and run
entirely within cloud free-tier limits — and speaks up the moment a decision would
require a paid tier, explaining what triggers the cost and roughly how much.

It's advisory, not a scaffolder: it steers the conversation while you're deciding on a
stack, rather than handing back a boilerplate repo. A `/free-tier-audit` command is
included for a point-in-time check of an architecture you've already started.

Works equally well for a hobbyist prototyping a side project and a consultant scoping a
client MVP — the guidance doesn't change, only whose money is on the line does.

## What it does

Once installed, the `free-tier-architect` skill activates automatically whenever you're
discussing application architecture, choosing a database or hosting platform, or asking
how to build/host/deploy something — you don't need to say "free tier" or "budget" out
loud for it to kick in.

**Before:**

> **You:** I need to store user profiles and session data — thinking Cosmos DB for
> profiles and a separate Cosmos account for sessions so they don't collide.
>
> **Claude:** Sure, here's how to set up two Cosmos DB accounts...

**After (with `free-tier` installed):**

> **You:** I need to store user profiles and session data — thinking Cosmos DB for
> profiles and a separate Cosmos account for sessions so they don't collide.
>
> **Claude:** One thing before you do — Azure's Cosmos DB free tier (1000 RU/s + 25 GB)
> is granted **once per subscription**, not per account. A second Cosmos DB account gets
> no free grant at all; it bills from RU/s zero. Put profiles and sessions in separate
> **containers inside the same free-tier account** instead — same isolation, same $0.
> Want me to sketch that out?

## Current scope

- **Azure-first.** The `azure.md` reference is current and detailed (Static Web Apps,
  Cosmos DB serverless/free-tier, Functions consumption plan, and the combinations of
  those three that cover most small apps).
- **AWS, GCP, and Vercel+Supabase** have stub reference files acknowledging they're not
  yet covered — the skill will say so rather than improvise numbers it hasn't verified.
- **Advisory, not generative.** This version steers decisions in conversation and audits
  existing designs. It does not generate Bicep/Terraform or scaffold a repo — that's a
  plausible v2 (see [Roadmap](#roadmap)).
- Limits and pricing get stale. The skill is instructed to verify current numbers against
  official Microsoft Learn / Azure pricing pages via web search before quoting specifics
  in a consequential conversation, rather than relying solely on the baked-in reference
  data — see `skills/free-tier-architect/references/azure.md` for its "Last verified" date.

## Install

```
/plugin marketplace add brandonmagaddino/free-tier
/plugin install free-tier@free-tier
```

(This repository is its own marketplace — no separate marketplace repo needed.)

## Usage

Nothing to configure. Once installed, just talk through what you're building:

```
/free-tier-audit
```

Run this in an existing project (or paste a description of your architecture) for a
report of what's currently free, what's paid and why, and a low-cost fix for anything
that's paid.

## Trust & Permissions

No telemetry. No phone-home. This plugin is markdown instructions and one slash command —
it never makes a network call to any server we control.

| | Detail |
|---|---|
| **Reads** | The current conversation, and — only if you're running `/free-tier-audit` inside a repo — config/manifest files that hint at deployed Azure resources (`staticwebapp.config.json`, `host.json`, `*.bicep`, `appsettings*.json` key names, CI workflow files). Never reads secret *values*. |
| **Writes** | Nothing. This plugin never edits, creates, or deletes files. |
| **Network** | Only the public internet, only via Claude's normal web search, only to check current Azure pricing/limits against Microsoft Learn and the Azure pricing pages when a cost-sensitive answer calls for it. No calls to any third-party server. |
| **Credentials / deployments** | Never touches either. This plugin can't deploy anything and never asks for a credential. |

See [SECURITY.md](SECURITY.md) for the full disclosure policy and contact.

## Disclaimer

This plugin gives architectural guidance based on publicly available pricing information,
which can be outdated, incomplete, or wrong. It does not guarantee that any recommended
design will stay within a free tier, and you are solely responsible for monitoring your
own cloud spend and verifying pricing before deploying. See [LICENSE](LICENSE) — provided
"as is," with no warranty and no liability for damages, including unexpected charges.

## Roadmap

- Additional cloud reference files: AWS, GCP, Vercel + Supabase (see below — this is the
  easiest way to contribute).
- Possible v2: optional Bicep/Terraform generation for the recommended free-tier shape,
  once the advisory behavior above has proven itself useful on its own.

## Contributing a new cloud

Each cloud gets one reference file under `skills/free-tier-architect/references/`. Follow
the shape of `azure.md`:

1. Scannable, decision-tree-style entries — not prose paragraphs. This file gets read
   into context, so keep it dense and skimmable.
2. A visible **"Last verified: `<date>`"** line at the top.
3. A dedicated section for services with **no meaningful perpetual free tier**, so the
   skill can flag them immediately rather than have to infer it.
4. A "common combos and their gotchas" section covering the 2-3 typical stacks on that
   cloud and where they leave free tier.

Then update `SKILL.md`'s "Cloud scope" section to point at the new file instead of listing
it as a stub, and remove the "not yet covered" notice from the reference file itself.

## License

[MIT](LICENSE)
