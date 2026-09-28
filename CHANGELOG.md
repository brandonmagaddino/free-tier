# Changelog

All notable changes to this project are documented here.

## [0.2.0] - 2026-09-28

### Added
- Cursor support: `.cursor-plugin/plugin.json` manifest.
- Codex support: portable root `plugin.json` manifest and
  `.agents/plugins/marketplace.json`, so the repo installs via
  `codex plugin marketplace add brandonmagaddino/free-tier`.
- CI check that all manifests are valid JSON and share the same version.

### Changed
- `/free-tier-audit` is now the `free-tier-audit` skill (`skills/free-tier-audit/`)
  instead of a command, so it works in all three tools. It is still invoked as
  `/free-tier-audit` in Claude Code and Cursor; use `$free-tier-audit` in Codex.
- README, SECURITY, and skill text made tool-neutral; skill notes the fallback when
  the host tool has web search disabled.

## [0.1.0] - 2026-08-17

### Added
- Initial release of the `free-tier-architect` skill (Azure-first): decision framework
  for frontend/data/API choices, "you're about to leave free tier" trigger conditions,
  verify-before-quoting-numbers behavior, and a scaling/multi-tenant caveat.
- `azure.md` reference: Static Web Apps, Cosmos DB free tier, Functions consumption plan,
  services with no perpetual free tier, and common combos with their gotchas.
- Stub reference files for AWS, GCP, and Vercel + Supabase.
- `/free-tier-audit` command for auditing a described or in-repo architecture against
  Azure free-tier limits.
- Self-hosted marketplace manifest so the repo installs directly via
  `/plugin marketplace add`.
