---
name: wrap-up
description: End-of-session wrap-up - updates vault, creates handoff note
---

You are wrapping up the current work session. Follow these steps exactly:

1. Read `.claude/vault/00_Meta/execution-plan.md` for the task index.
2. Read the latest handoff note in `.claude/vault/00_Meta/handoffs/` to determine the current session number (increment by 1 for the new handoff).
3. Review the conversation history for this session. Identify:
   a. What tasks were worked on and their new status (completed, in-progress, blocked)
   b. What decisions were made
   c. What project source files were created or modified (including by OMC agents delegated during this session)
   d. Any new blockers or open questions
   e. What vault files need updating to reflect the work done
   f. Whether any procedural skills were followed — and if so, whether any steps were missing, unclear, or had gotchas
   g. Which OMC agents were used and what they accomplished

4. Update task files and the execution plan index:
   - For each task worked on, update its individual file in `.claude/vault/00_Meta/tasks/TASK-NNN.md`:
     - Change the status
     - Add notes about what was discovered or accomplished
     - Note which OMC agents were used for the work
     - If a skill was used, fill in the "Skill Feedback" section with assertion pass/fail data and any gotchas
   - Update `.claude/vault/00_Meta/execution-plan.md` index table to reflect new statuses
   - If new tasks were identified during the session, create new task files AND add them to the index with proper dependency links

5. Update any department content files that are now outdated due to this session's work:
   - If architecture changed, update `01_RnD/architecture.md`
   - If new patterns were established, update `03_Engineering/patterns.md`
   - If product requirements evolved, update `02_Product/requirements.md`
   - Update the relevant index file's "Recent Updates" section
   - **Index file check:** After updating any index file, verify it is still under 40 lines. If it exceeds 40 lines, extract detail into a linked file and keep the index as a navigation hub only.

6. Track skill usage (if applicable):
   - If any skills from `.claude/vault/03_Engineering/skills/` were followed this session, append a run entry to `.claude/vault/03_Engineering/skills/skill-runs.md` with:
     - Which skill was used, which task it was used for
     - Which assertions passed/failed (if an eval file exists for that skill)
     - Any gotchas discovered (steps that were unclear, missing, or wrong)
   - If a new recurring procedure was discovered this session that doesn't have a skill file yet, ask the user: "I noticed a repeatable procedure for [X]. Should I create a skill file for it?"

7. If any significant decisions were made, create decision record files in the appropriate `decisions/` folder using the decision record schema.

8. Create a new handoff note at `.claude/vault/00_Meta/handoffs/NNN-YYYY-MM-DD.md` (where NNN is the new session number, zero-padded to 3 digits, and YYYY-MM-DD is today's date) following the handoff note schema exactly.

9. Present a wrap-up summary:

**Session NNN Complete**

**Tasks Updated:**
- (list each task ID, old status -> new status)

**Vault Files Modified:**
- (list each vault file that was created or updated)

**Agents Used:** (list OMC agents that were delegated to during this session, or "None")

**Skills Used:** (list skills executed this session, with pass rates, or "None")

**New Skills Suggested:** (list any new procedures discovered, or "None")

**Handoff Written:** (path to new handoff note)

**Next Session Should:** (1-2 sentence recommendation)
