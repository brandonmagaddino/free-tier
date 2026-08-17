# Changelog

All notable changes to this project are documented here.

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
