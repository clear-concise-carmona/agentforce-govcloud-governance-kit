# Agentforce Field Access Worksheet

**This is the free lead magnet template for this repo.** Fill it in for every field your Agentforce agent's permission set can read or write, before you route the agent for ISSO sign-off.

SYNTHETIC — the rows below are a filled-in example using a fake org. Replace with your own agent's actual fields.

## How to fill this in

`scripts/field_access_report.py` (see below) queries `FieldDefinition.SecurityClassification` and `FieldDefinition.ComplianceGroup` via the Tooling API for the objects you name, and pre-fills columns 1, 3, and 4 where your org has populated that metadata. **Two things it cannot do for you:**

1. It cannot tell you whether a field is actually readable/editable by the agent's specific permission set — cross-reference against your permission set XML (see `docs/agent-permission-set-template.permissionset-meta.xml`) by hand, or extend the script for your org's permission set naming convention.
2. It cannot fill in a row if your org has never populated Data Sensitivity Level / Compliance Categorization for that field. Most orgs haven't classified every field. When the script returns blank for a field, fill it in manually — don't leave it blank in this worksheet.

If you'd rather not run the script at all, this worksheet works as a pure manual template. Every column can be filled in by hand from Setup > Object Manager > [Object] > Fields & Relationships > [Field] > Data Classification.

## Worksheet

| Object.Field | Agent access (Read / Edit / None) | Data Sensitivity Level (Setup classification) | Compliance Categorization (Setup classification) | Source of this row | Reviewed by | Date |
|---|---|---|---|---|---|---|
| Case.Status | Read | Internal | — | Auto (Tooling API) | — | 2026-09-14 |
| Case.Description | Read | Confidential | PII | Auto (Tooling API) | — | 2026-09-14 |
| Contact.Email | None (excluded from agent scope) | Confidential | PII, CCPA | Auto (Tooling API) | — | 2026-09-14 |
| Case.CustomHealthNote__c | Read | *(not classified in this org — filled manually)* | HIPAA (manual: contains protected health info per business owner) | Manual | Jane ISSO | 2026-09-14 |

## Columns explained

- **Object.Field**: the exact API name, matching what's in the agent's permission set.
- **Agent access**: what the permission set actually grants. This should match `docs/agent-permission-set-template.permissionset-meta.xml` exactly, not what you intend to grant eventually.
- **Data Sensitivity Level**: pulled from `FieldDefinition.SecurityClassification` (Setup label "Data Sensitivity Level") when your org has set it. Values: Public, Internal, Confidential, Restricted, MissionCritical.
- **Compliance Categorization**: pulled from `FieldDefinition.ComplianceGroup` (Setup label "Compliance Categorization") when set. Values include CCPA, COPPA, GDPR, HIPAA, PCI, PersonalInfo, PII.
- **Source of this row**: "Auto (Tooling API)" if the script filled it, "Manual" if a human filled it in. Never leave this blank.
- **Reviewed by / Date**: who signed off that this row is accurate, and when. This worksheet goes stale the moment someone adds a field to the agent's scope without updating it.

## Why this exists

An agent's permission set is a list of API names. It doesn't tell you, at a glance, whether field #14 on that list holds a Social Security number. This worksheet is the translation layer between "what can the agent technically read" and "what kind of data is that, and did anyone with authority sign off on the agent reading it." Government Cloud Plus's authorization boundary is about which systems and data flows are inside FedRAMP High / DoD IL2 scope — an agent quietly reading a Restricted or HIPAA-tagged field it didn't need is exactly the kind of gap that boundary reviews are meant to catch before an ATO decision, not after.
