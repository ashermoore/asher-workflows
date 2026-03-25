---
name: status
description: Read-only project status overview with dependency chain and critical path
---

Read `.claude/vault/00_Meta/execution-plan.md` and the latest handoff note in `.claude/vault/00_Meta/handoffs/`.

Present the current project state:

**Project Status**

| Status | Count | Tasks |
|--------|-------|-------|
| Completed | N | TASK-001, TASK-002, ... |
| In Progress | N | TASK-XXX, ... |
| Blocked | N | TASK-YYY (blocked by TASK-ZZZ), ... |
| Unblocked / Ready | N | TASK-AAA, ... |
| Not Started (blocked) | N | TASK-BBB, ... |

**Dependency Chain:** Show which completed tasks have unlocked which tasks.

**Critical Path:** Identify the longest chain of uncompleted dependent tasks — this is the bottleneck.

**Last Activity:** (date and summary from latest handoff note)

**Skills Health:** Read `.claude/vault/03_Engineering/skills/skill-runs.md` if it exists. Report any skills with pass rate below 100% that may need `/refine-skills`.

Do NOT modify any vault files. This command is read-only.
