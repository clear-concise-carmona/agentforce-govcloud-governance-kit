# Model Pin Checklist

This is a **manual re-check workflow**, not a hardcoded table. Per-model availability in Government Cloud Plus changes with every Salesforce release, and hardcoding a snapshot here would go stale within weeks. This checklist tells you what to check, where, and how often. It does not tell you which models are available today.

## Why this matters (the exact reason Salesforce gives)

From Salesforce's own Agentforce in Government Cloud documentation:

> "LLM models can appear in Einstein Studio and Prompt Builder before US government approval, because FedRAMP approval processes require deployment to Salesforce Government Cloud production orgs for auditing purposes. Salesforce updates this page when new models become available."
> — [Agentforce in Government Cloud](https://help.salesforce.com/s/articleView?id=ind.government_cloud_agentforce.htm&language=en_US&type=5), Salesforce Help

Read that again: a model can show up as a selectable option in your org's Einstein Studio or Prompt Builder UI **before** it has US government approval. The UI does not stop you from picking an unapproved model. Nothing in the product prevents an admin from selecting a model that hasn't cleared FedRAMP review yet. That gap is exactly what this checklist exists to close.

Also from the same source: "Admins control which LLMs are available in an org. See Manage Model Provider Access." Admin control is the enforcement mechanism here, not a warning banner. If nobody restricts model provider access, nothing stops an unapproved model from being picked.

## The checklist

Run this before enabling any Agentforce agent in a Government Cloud Plus org, and again after every Salesforce release (see README's re-verify cadence).

- [ ] **1. Open the live Salesforce page**, not this repo, not a cached copy: [Agentforce in Government Cloud](https://help.salesforce.com/s/articleView?id=ind.government_cloud_agentforce.htm&language=en_US&type=5). Note today's date.
- [ ] **2. Find the LLM availability table on that page** and record, for each model your agent's Prompt Builder / Einstein Studio configuration could select: model name, provider, and its listed availability status (e.g., Available / Not Available). Footnoted models ("This option appears as a selection but isn't within the authorization boundary in Government Cloud") count as Not Available for authorization purposes even though they appear selectable.
- [ ] **3. Cross-check against your org's actual Model Provider Access settings** (Setup > Einstein > Model Provider Access, or the equivalent Government Cloud Plus setup path). Confirm every model an admin could pick is one that step 2 marked Available.
- [ ] **4. If your agent uses the Atlas Reasoning Engine**, confirm which model backs it. As of the last verified check (Sept 14, 2026), Salesforce states "The Atlas Reasoning Engine supports only Azure OpenAI (GPT 4o (Omni)) in Government Cloud" per the same Help article above — re-verify this line specifically, since reasoning-engine model support is exactly the kind of detail that changes with a release.
- [ ] **5. Restrict Model Provider Access** in your org so only confirmed-Available models are selectable by anyone, not just by convention. Document who has permission to change this setting.
- [ ] **6. Record the result** in your own model pin log (see template row below) with the date you checked, who checked it, and the source URL. Attach this log to the ISSO acceptance memo for the agent.
- [ ] **7. Re-run this checklist after every Salesforce release** (three per year: Spring, Summer, Winter) and any time you add a new agent, topic, or Prompt Builder template that could select a different model.

## Model pin log template

SYNTHETIC example row — replace with your own:

| Date checked | Checked by | Model | Provider | Status per live page | Used by | Source URL |
|---|---|---|---|---|---|---|
| 2026-09-14 | J. ISSO | Claude Sonnet 4.5 | Anthropic | Available | SYNTHETIC-SummarizeCaseHistory action | https://help.salesforce.com/s/articleView?id=ind.government_cloud_agentforce.htm&language=en_US&type=5 |

Do not copy example LLM availability rows out of this repo's README or VERIFICATION.md and treat them as current. They are dated snapshots from when this repo's sources were checked, not live data.
