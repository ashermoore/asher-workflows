---
description: Resume work on a project - loads vault context and presents session briefing
---

You are resuming work on this project. Follow these steps exactly:

1. Read `.claude/vault/00_Meta/project-brief.md` to understand the project.
2. Read `.claude/vault/00_Meta/execution-plan.md` to see all tasks, their statuses, and dependencies.
3. For any tasks with status "in-progress" or that are unblocked (status "not-started" with all dependencies "completed"), read their individual task files from `.claude/vault/00_Meta/tasks/`.
4. Find the most recent handoff note in `.claude/vault/00_Meta/handoffs/` (the file with the highest session number) and read it.
5. Read the index files for departments relevant to the unblocked tasks.

Then present a session briefing:

**Last Session Summary:** (2-3 bullet points from the handoff note)

**Current State:**
- Completed: N/M tasks
- In Progress: (list task IDs and titles)
- Blocked: (list task IDs, what they're blocked by)
- Unblocked and Ready: (list task IDs and titles)

**Recommended Next Action:** (which unblocked task to start with and why)

**Relevant Context Loaded:** (list which vault files you read)

Do NOT modify any vault files. This command is read-only.
