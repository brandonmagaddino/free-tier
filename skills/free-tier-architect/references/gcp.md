# GCP Free Tier Reference

**Status: not yet covered.**

This skill is Azure-first today. GCP-specific free-tier guidance (Firebase Hosting,
Firestore's free quota, Cloud Functions/Cloud Run's always-free tier, App Engine's free
daily quota, etc.) hasn't been written yet.

If the user is targeting GCP, say so plainly rather than improvising specifics from
general knowledge — general knowledge on free-tier limits goes stale fast and this file
hasn't been verified the way `azure.md` has. Give best-effort direction (GCP's shape is
broadly similar: static hosting + managed NoSQL + serverless functions, and GCP's
always-free tier is generally more generous than AWS's 12-month-limited one), but don't
quote exact numbers you haven't confirmed against Google Cloud's current pricing pages.

Contributions welcome — see the README's "Adding a new cloud" section for the expected
shape of this file (decision-tree style entries with a "Last verified" date, matching
`azure.md`).
