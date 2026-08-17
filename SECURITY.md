# Security Policy

`free-tier` is a Claude Code plugin made up of markdown skill/command instructions and
one slash command. It is not a service, has no backend, and ships no executable code
beyond what Claude Code itself already runs (the markdown files are prompts, not scripts).

## What this plugin can access

- **The current conversation** — same read access any Claude Code skill has to what
  you've typed and what's in context.
- **Local files, but read-only, and only when you run `/free-tier-audit` inside a repo** —
  it looks at config/manifest/IaC files that hint at deployed Azure resources
  (`staticwebapp.config.json`, `host.json`, `*.bicep`/`*.tf`, `appsettings*.json` /
  `.env*` **key names only, never values**, CI workflow files). This is the same file
  access any Claude Code session already has in your working directory — the plugin
  doesn't expand it.
- **The public internet, via Claude's normal web search** — used to verify current Azure
  pricing/limits against Microsoft Learn and official Azure pricing pages before quoting
  specific numbers in a cost-sensitive conversation. This goes through Claude Code's
  existing web search capability, not a network call this plugin makes on its own.

## What this plugin never does

- **No telemetry, no phone-home.** Nothing in this plugin sends data anywhere. There is
  no analytics endpoint, no update-check ping, no server we control that it talks to.
- **No writes.** It never creates, edits, or deletes a file. `/free-tier-audit` produces
  a report in the conversation; it does not modify your repo.
- **No deployments.** It never runs `az`, `terraform`, `bicep`, or any deployment tooling.
- **No credentials.** It never reads secret values, never asks for one, and has no
  mechanism to transmit one anywhere if it somehow encountered one in a file it scanned.

## Reporting a vulnerability or concern

If you find a way this plugin's instructions could be made to behave outside what's
described above — or a way its guidance itself could lead to a genuine security issue
(e.g., recommending an insecure default) — please open a
[GitHub issue](https://github.com/brandonmagaddino/free-tier/issues) on this repository.

For anything you'd rather not post publicly, email **brandonmagaddino@gmail.com**. We'll
acknowledge reports within a few business days.

## Disclaimer

This plugin gives architectural guidance based on publicly available pricing information,
which can be outdated, incomplete, or wrong. It does not guarantee that any recommended
design will stay within a free tier, and you are solely responsible for monitoring your
own cloud spend and verifying pricing before deploying. See [LICENSE](LICENSE) —
provided "as is," with no warranty and no liability for damages, including unexpected
charges.
