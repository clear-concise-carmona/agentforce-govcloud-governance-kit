# Sample: Filled-In Worksheet Set for a Fictional Agent

**SYNTHETIC — this entire example uses a fake agency, fake org, and fake data. None of this is a real org.**

This shows how the templates in `docs/` fit together for one fictional agent, "Case Status Assistant," at a fictional agency, "Department of Example Affairs" (DEA — not real).

## 1. Permission set (summary of the filled template)

Agent integration user: `svc.agentforce.casestatus@dea.example.gov.invalid`
Permission set: `Agentforce Agent - Case Status Assistant - Least Privilege`
- Case: Read only, no Modify All / View All
- Contact: Read only, restricted to Name and AccountId (not Email or Phone)
- No Apex class access needed (declarative actions only)

## 2. Field access worksheet (excerpt)

| Object.Field | Agent access | Data Sensitivity Level | Compliance Categorization | Source | Reviewed by | Date |
|---|---|---|---|---|---|---|
| Case.Status | Read | Internal | - | Auto (Tooling API) | M. Rivera (ISSO) | 2026-09-10 |
| Case.CaseNumber | Read | Internal | - | Auto (Tooling API) | M. Rivera (ISSO) | 2026-09-10 |
| Contact.Name | Read | Internal | - | Auto (Tooling API) | M. Rivera (ISSO) | 2026-09-10 |
| Contact.Email | None (explicitly excluded from agent scope) | Confidential | PII, CCPA | Auto (Tooling API) | M. Rivera (ISSO) | 2026-09-10 |

Decision: Contact.Email was excluded from the agent's permission set specifically because it's tagged PII and the agent's use case (reading case status) doesn't require it.

## 3. Action inventory (excerpt)

| action_name | inside_or_exits_boundary | authorization_basis |
|---|---|---|
| LookupCaseStatus | Inside boundary | Standard object query, no external call |
| NotifyRequesterByEmail | Needs review at design time, resolved to Inside boundary | Uses Salesforce's own Email Relay within Government Cloud Plus, not an external SMTP provider - confirmed with DEA's Salesforce admin before approval |

## 4. Model pin log (excerpt)

| Date checked | Checked by | Model | Status per live page | Used by |
|---|---|---|---|---|
| 2026-09-10 | M. Rivera (ISSO) | Anthropic Claude Sonnet 4.5 | Available | Case summarization prompt |

## 5. ISSO memo outcome

Approved for limited pilot: 15 users, 30-day review scheduled for 2026-10-10, per the filled acceptance memo. Known gap noted in the memo: agent's fallback behavior when Case.Status is null had not been tested at approval time and was flagged for the 30-day review.

---

This example exists to show the workflow end to end. It is not a template to copy field-for-field into a real ISSO submission - your agent's actual objects, fields, and risk profile will differ.
