---
description: Record an architectural or product decision in the vault
---

You are recording an architectural or product decision.

Ask the user (if not already clear from context):
1. What is the decision about? (short title)
2. What department does it belong to? (RnD, Product, Engineering)
3. What options were considered?
4. What was decided and why?

Then:
1. Create a decision record file at `.claude/vault/[department]/decisions/YYYY-MM-DD-slug.md` following the decision record schema.
2. Update the relevant department index file to reference the new decision in its "Recent Updates" section.
3. If the decision affects the execution plan (e.g. changes approach for a task, unblocks something, introduces new work), update `.claude/vault/00_Meta/execution-plan.md` accordingly.
4. If the decision supersedes a previous decision, update the old decision's status to "superseded" and add a link to the new one.

Present:

**Decision Recorded:** [[YYYY-MM-DD-slug]]
**Department:** (department name)
**Impact on Plan:** (any tasks affected, or "none")
