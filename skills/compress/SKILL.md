---
name: compress
description: Compress vault by archiving old handoffs and pruning completed tasks
---

You are compressing the vault to prevent unbounded growth. This should be run when the handoffs/ folder has 10+ files.

Follow these steps exactly:

## Step 1: Assess Current Size

1. List all files in `.claude/vault/00_Meta/handoffs/`.
2. Read `.claude/vault/00_Meta/execution-plan.md` and count completed tasks.
3. Report: "Found N handoff notes and M completed tasks."

If there are fewer than 8 handoff notes (excluding archive.md), report "Vault is not large enough to need compression" and stop.

## Step 2: Archive Old Handoffs

Keep the 5 most recent handoff notes as individual files. For all older handoffs:

1. Read each old handoff note.
2. Extract: key decisions, milestones reached, and any patterns or recurring blockers.
3. Append a new section to `.claude/vault/00_Meta/handoffs/archive.md` summarising these sessions:
   - Session range (e.g. "Sessions 001-015")
   - Date of archival
   - Consolidated key decisions (with links to decision records if they exist)
   - Major milestones
   - Patterns observed (recurring blockers, workflow insights)
4. Delete the individual handoff files that were archived.

## Step 3: Prune Execution Plan

In `.claude/vault/00_Meta/execution-plan.md`:

1. Find all tasks with status "completed" that are NOT depended on by any non-completed task.
2. Move these tasks from the main Task Index table to the "Archived Tasks (Completed)" table with just their ID, title, and completion date.
3. Keep all in-progress, blocked, and not-started tasks in the main table.
4. Keep completed tasks that other active tasks depend on in the main table (they're still needed for dependency resolution).
5. The individual task files in `00_Meta/tasks/` are NOT deleted — they remain as historical records. Only the index table is pruned.

Also prune skill-runs.md if it exists:
6. In `.claude/vault/03_Engineering/skills/skill-runs.md`, consolidate run entries older than the 20 most recent into the summary table. Remove the individual old run entries but preserve their aggregate data in the summary.

## Step 4: Verify

- Confirm the 5 most recent handoffs are still intact.
- Confirm archive.md has the new section.
- Confirm the execution plan index still has valid dependency references (no dangling references to archived tasks).
- Confirm no information was permanently lost (decisions are in decision records, milestones are in archive, task files still exist).

Present:

**Vault Compressed**

**Handoffs Archived:** N sessions compressed into archive.md
**Handoffs Remaining:** 5 most recent
**Tasks Pruned:** M completed tasks moved to archive section in index
**Tasks Active:** N tasks still in main index table
**Skill Runs Consolidated:** N old run entries folded into summary (or "N/A")

**Vault Size Before:** (approximate file count)
**Vault Size After:** (approximate file count)
