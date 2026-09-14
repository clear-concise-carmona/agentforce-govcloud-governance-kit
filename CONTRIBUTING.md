# Contributing

This repo is templates and a read-only local script, not a Salesforce package. Contributions are welcome, with a few rules that matter more here than in a typical repo because the subject is federal compliance.

## Rules

1. **Synthetic data only.** Any example, sample worksheet, or test fixture must use fake org names, fake user names, and fake data. Label it `SYNTHETIC — not a real org` in the file itself. Do not submit a PR that includes a real org ID, real IP range, real user data, or a screenshot of a real org.
2. **No Salesforce documentation redistribution.** Do not paste Salesforce's authorization tables, full Help article text, or product tables into this repo. Link to the specific Help article by ID. Short direct quotes are fine only when they carry a citation matching the pattern already used in `VERIFICATION.md`.
3. **Every claim about FedRAMP, DoD Impact Levels, or Agentforce model availability needs a source URL and a date you checked it.** If you're adding or editing a claim like this, add or update the row in `VERIFICATION.md` with CONFIRMED / INFERRED / UNVERIFIED and the source you checked. Don't guess.
4. **This repo does not install anything into a Salesforce org.** Any script addition has to stay read-only against the Salesforce CLI (`sf`) or documented REST/Tooling/Metadata APIs, writing output to local files only. No metadata deployments, no managed or unmanaged packages.
5. **Match the existing tone.** Plain, direct language. No hype words. State what you don't know instead of smoothing over it.

## How to submit a change

1. Fork the repo.
2. Make your change in a branch.
3. If you touched a compliance claim or added a dataset row, update `VERIFICATION.md` in the same PR.
4. Open a PR describing what changed and why, with source links for any factual claim.

## Reporting a stale fact

Salesforce changes its Government Cloud product tables and Agentforce model lists regularly. If you notice something in this repo is out of date, open an issue with the current Salesforce Help article URL and what changed. That's the single most useful contribution this repo can get.
