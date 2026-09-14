# ISSO Acceptance Memo Template

Fill this in and route it to your Information System Security Officer (ISSO) or equivalent authorizing role before enabling an Agentforce agent in a Government Cloud Plus org. This is a starting draft, not a legal or compliance document by itself — see the disclaimer in the main README.

SYNTHETIC — the bracketed fields below are placeholders. Replace every bracket before sending.

---

**Memo: Request for Acceptance — Agentforce Agent Deployment**

**To:** [ISSO / Authorizing Official name]
**From:** [Agency AI lead / Salesforce admin name]
**Date:** [date]
**Org:** [Government Cloud Plus org name/environment — do not include real org IDs in any copy of this memo that leaves your organization's internal systems]
**Agent name:** [agent name]

## 1. What this agent does

[Plain-language description: what user-facing problem does this agent solve, what topics does it handle, what actions can it take. No jargon — this section should be readable by someone who has never opened Setup.]

## 2. Scope of access

- Permission set(s) assigned: [link to your filled-in copy of `docs/agent-permission-set-template.permissionset-meta.xml`]
- Objects and fields the agent can read or write: [link to your filled-in `docs/field-access-worksheet.md`]
- Data Sensitivity Levels / Compliance Categorizations touched: [summarize the highest sensitivity level touched, e.g. "Confidential, includes one PII-tagged field"]

## 3. Actions and boundary review

- Action inventory: [link to your filled-in copy of `docs/action-inventory-template.csv`]
- Number of actions that exit the authorization boundary (call something outside FedRAMP High / DoD IL2 scope): [count]
- If any actions exit the boundary, list them and their own authorization/interconnection status: [detail, or "N/A — no actions exit the boundary"]

## 4. Model pinning

- Model pin checklist completed on: [date] by [name] — see `docs/model-pin-checklist.md`
- Model(s) this agent's Prompt Builder/Einstein Studio configuration uses: [list]
- Confirmed Available per the live Salesforce Government Cloud model page as of the date above: [yes/no, with source URL]

## 5. Human oversight

- [Describe: is there a human-in-the-loop review step? What triggers escalation to a person? Who is the accountable human if the agent gets something wrong?]

## 6. Known gaps or risks

[State plainly what you don't know yet or haven't finished checking. Don't smooth this over — an ISSO needs the real gap list, not a clean-looking memo. Example: "We have not yet confirmed whether CaseComment.CommentBody is populated with any HIPAA-relevant text in production; flagging for a follow-up data sample review."]

## 7. Requested action

[ ] Approve for production use
[ ] Approve for limited pilot only (describe limits)
[ ] Request changes before resubmission

**ISSO / Authorizing Official signature:** _______________________
**Date:** _______________________

---

*This template is a starting draft for your own ISSO or authorizing official to review and adapt. It is not a substitute for your organization's actual authorization process, and completing it does not itself constitute an ATO or any other compliance decision. See the main README disclaimer.*
