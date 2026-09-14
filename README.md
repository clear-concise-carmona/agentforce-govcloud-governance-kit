# agentforce-govcloud-governance-kit

Templates and checklists for deploying **Agentforce** inside **Salesforce Government Cloud Plus** without losing the authorization boundary: an agent permission set template, a field access worksheet, an action inventory, a model pinning checklist, and an ISSO acceptance memo.

**Sources verified as of September 14, 2026.** See [VERIFICATION.md](VERIFICATION.md) for the full claims table with citations.

**Part of a five-repo Government Cloud tool set from Clear Concise Consulting.** Browse the full source index and the other tools at [awesome-salesforce-government-cloud](https://github.com/clear-concise-carmona/awesome-salesforce-government-cloud).

## 1. Disclaimer

This repo is independent work by [Clear Concise Consulting](https://www.clearconciseconsulting.com), not officially affiliated with, certified by, or endorsed by Salesforce, Inc.

This is not legal, compliance, or ATO (Authority to Operate) advice. Every template here is a starting draft. Any authorization or compliance decision for your agent has to be made by your own organization's ISSO (Information System Security Officer) or Authorizing Official, checked against the live, current Salesforce documentation, not against this repo. Nothing here can make an authorization decision for you, and nothing in this repo should be read as implying it can.

## 2. Why governance precedes go-live

Government Cloud Plus's value is the authorization boundary: a defined set of systems, data flows, and products that have actually been evaluated against FedRAMP High and DoD IL2 (or IL5 for Government Cloud Plus - Defense) requirements ([Government Cloud Available Products and Features](https://help.salesforce.com/s/articleView?id=000396813&language=en_US&type=1)). An Agentforce agent is a new kind of thing inside that boundary: it doesn't just store or display data, it reads data, calls a model, and takes actions, sometimes automatically. Each of those three things (read, model, action) is a place the boundary can quietly get crossed if nobody checks first: a field the agent shouldn't read, a model that isn't authorized yet, an action that calls something outside the boundary. None of that shows up as an error in Setup. It shows up later, in an audit or an incident, when it's a lot more expensive to fix.

The templates in this repo exist to make those three checks routine, before an agent goes live, instead of after.

## 3. What an agent is (in plain language)

Strip away the product marketing and an Agentforce agent is four things:

- **A user**: agents run as a specific Salesforce user (an integration or run-as user), with whatever permission set that user has been assigned. Same rules as any other user, no more, no less.
- **Data**: whatever objects and fields that user's permission set can read or write.
- **A model**: the LLM the agent's Prompt Builder or Einstein Studio configuration is pointed at, which does the actual reasoning over the data it's given.
- **Actions**: things the agent can actually do, from reading a field, to updating a record, to calling something entirely outside Salesforce.

Every governance question in this repo maps to one of these four things: is the user's permission set least-privilege (`docs/agent-permission-set-template.permissionset-meta.xml`), do we know what the data actually is (`docs/field-access-worksheet.md`), is the model authorized (`docs/model-pin-checklist.md`), and does any action leave the boundary (`docs/action-inventory-template.csv`).

## 4. Templates

| Template | File | What it's for |
|---|---|---|
| Agent permission set | [docs/agent-permission-set-template.permissionset-meta.xml](docs/agent-permission-set-template.permissionset-meta.xml) | Least-privilege permission set metadata for an agent's integration user, no profile inheritance |
| Field access worksheet | [docs/field-access-worksheet.md](docs/field-access-worksheet.md) | Translates "what fields can the agent reach" into "what kind of data is that, and did anyone sign off" (this repo's free lead magnet — see the CTA at the bottom) |
| Action inventory | [docs/action-inventory-template.csv](docs/action-inventory-template.csv) | Logs every agent action with an inside-boundary / exits-boundary column |
| Model pin checklist | [docs/model-pin-checklist.md](docs/model-pin-checklist.md) | A manual re-check workflow against the live Salesforce model availability page |
| ISSO acceptance memo | [docs/isso-acceptance-memo-template.md](docs/isso-acceptance-memo-template.md) | A fill-in-and-route memo for ISSO sign-off before an agent goes live |
| Data flow diagram | [docs/data-flow-diagram-template.md](docs/data-flow-diagram-template.md) | A one-page Mermaid diagram template: user → agent → data → model → action, with the boundary line drawn in |
| Worked example | [examples/sample-filled-worksheet.md](examples/sample-filled-worksheet.md) | A fully filled-in, synthetic example tying all five templates together for one fictional agent |

## 5. The field access worksheet (and the script behind it)

The field access worksheet started as a request for full automation: read an agent's permission set, read Salesforce's Data Sensitivity Level and Compliance Categorization metadata for every field it touches, and produce a finished report. That's partly possible and partly not, and we're not going to pretend otherwise.

What's actually true: `FieldDefinition.SecurityClassification` (Data Sensitivity Level) and `FieldDefinition.ComplianceGroup` (Compliance Categorization) are queryable via the Tooling API ([Data Classification Metadata Fields](https://help.salesforce.com/s/articleView?id=platform.data_classification_metadata_fields.htm&language=en_US&type=5)), so `scripts/field_access_report.py` runs a real, read-only SOQL query against a target org and pre-fills what it can:

```bash
sf org login web --alias my-govcloud-org
python3 scripts/field_access_report.py --target-org my-govcloud-org --object Case --object Contact --output field-access-report.md
```

What it can't do: most orgs haven't classified every field, so a lot of rows will come back blank. It also has no way to know which specific fields your agent's permission set can actually touch, since that's a separate cross-reference against the permission set XML, not something `FieldDefinition` metadata tells you by itself. The script says this plainly in its own output and comments. Use it to save time on the rows your org has already classified, and fill in the rest by hand in [docs/field-access-worksheet.md](docs/field-access-worksheet.md).

## 6. Model pinning

Government Cloud Plus's Agentforce authorization is real: "Agentforce in Government Cloud Plus is Federal Risk and Authorization Management Program (FedRAMP) High authorized" ([Agentforce in Government Cloud](https://help.salesforce.com/s/articleView?id=ind.government_cloud_agentforce.htm&language=en_US&type=5)). But that authorization is at the platform level, not a guarantee that every model an admin could pick in Setup is itself approved. Salesforce says this directly:

> "LLM models can appear in Einstein Studio and Prompt Builder before US government approval, because FedRAMP approval processes require deployment to Salesforce Government Cloud production orgs for auditing purposes. Salesforce updates this page when new models become available."
> — [Agentforce in Government Cloud](https://help.salesforce.com/s/articleView?id=ind.government_cloud_agentforce.htm&language=en_US&type=5), Salesforce Help

That's the whole reason [docs/model-pin-checklist.md](docs/model-pin-checklist.md) exists. It is deliberately **not** a table of which models are available right now. This repo doesn't hardcode that table anywhere, on purpose: Salesforce updates its model list with product releases, and a hardcoded table here would be wrong within a release cycle. The checklist instead walks you through checking the live page yourself, cross-checking your org's actual Model Provider Access settings, and logging what you found with a date and a source link, every release.

## 7. Acceptance memo

[docs/isso-acceptance-memo-template.md](docs/isso-acceptance-memo-template.md) pulls together the permission set, field access worksheet, action inventory, and model pin log into one document you route to your ISSO or Authorizing Official before enabling an agent. It has a section for known gaps and risks on purpose: an honest gap list is more useful to a reviewer than a memo that looks clean because it left the hard parts out.

## 8. Re-verify cadence

Salesforce ships three releases a year (Spring, Summer, Winter). Re-run the model pin checklist and re-check every field's classification after every release, and any time you add a new agent, topic, or action. Treat anything in this repo's docs older than one release as due for a re-check, not as current fact.

## 9. Verification log

Every specific FedRAMP, DoD Impact Level, and Agentforce claim this README makes is logged in [VERIFICATION.md](VERIFICATION.md), labeled CONFIRMED, INFERRED, or UNVERIFIED, with a source URL and date. If you're going to cite anything from this repo in your own ISSO submission, cite VERIFICATION.md's sources directly, not this README.

## What this repo does not do

Nothing in this repo installs into a Salesforce org. Every template here is a file you fill in locally, and `scripts/field_access_report.py` only reads metadata through the documented Tooling API via the Salesforce CLI (`sf`) and writes a local Markdown file. There are no managed or unmanaged packages, no AppExchange listing, and no metadata deployments anywhere in this repo. That's a real authorization-boundary reason, not just a caution: AppExchange packages are themselves subject to authorization-boundary review, and a read-only local script avoids that review entirely by never touching your org's configuration.

All sample data in this repo (`examples/`, and the example rows inside `docs/` templates) is synthetic and labeled "SYNTHETIC — not a real org." No client data, org identifiers, IP ranges, or screenshots of real orgs appear anywhere in this repo.

## License

Code and scripts (`scripts/`) are MIT licensed — see [LICENSE](LICENSE). Documentation, templates, and datasets (`docs/`, `examples/`, this README) are licensed under [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/).

## 10. If your Salesforce problem is really a boundary problem

If your Salesforce problem is really an evidence problem, the free AI Readiness Scorecard takes ten minutes and returns a prioritized list of what to fix first: https://www.clearconciseconsulting.com/scorecard. If you'd rather start with the field access worksheet specifically, it's in [docs/field-access-worksheet.md](docs/field-access-worksheet.md) — free, no signup. For a deeper review, the [Salesforce AI Data Readiness Assessment](https://www.clearconciseconsulting.com/) covers data quality, governance, access controls, automation, and documentation as the fourth layer of Clear Concise Consulting's governance method; if a gap in this checklist turns into a remediation project, that's the assessment to start from, with a governance remediation engagement as the follow-on scope. Maintained by [Clear Concise Consulting](https://www.clearconciseconsulting.com).
