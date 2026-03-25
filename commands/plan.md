---
description: Create or update the project execution plan with task breakdown
---

You are creating or updating the project execution plan.

1. Read `.claude/vault/00_Meta/project-brief.md` for project context.
2. Read `.claude/vault/00_Meta/execution-plan.md` if it exists.
3. Read all department index files to understand current project state.

BEFORE creating any tasks, follow the Clarification Protocol from the vault protocol (Section 1b of the build plan). Restate your understanding, identify gaps, ask questions one topic at a time, confirm assumptions, and reach 95%+ confidence. Do NOT generate tasks until the user confirms your final summary.

If the user provided a task description or goal, break it down into discrete tasks. For each task:
- Assign a sequential TASK-ID (continuing from the highest existing ID in the index)
- Identify dependencies (which tasks must complete before this one can start)
- Identify what this task blocks
- Assign to the appropriate department
- Write clear acceptance criteria
- Keep descriptions to 1-3 sentences
- Create an individual task file at `.claude/vault/00_Meta/tasks/TASK-NNN.md` following the task file schema
- Add the task to the execution plan index table

If the user wants to reorganise or re-prioritise, update accordingly. Never delete existing task files — mark superseded tasks as "completed" with a note explaining they were superseded.

After making changes, present:

**Execution Plan Updated**

**New Tasks Added:**
- (list with IDs and titles)

**Task Files Created:**
- (list file paths)

**Dependencies:**
- (show the dependency graph as a simple text tree or list)

**Unblocked and Ready to Start:**
- (list task IDs that can be started immediately)

Write the updated index to `.claude/vault/00_Meta/execution-plan.md` and individual task files to `.claude/vault/00_Meta/tasks/`.
