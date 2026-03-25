---
description: Cross-department vault consistency review - checks for contradictions, staleness, drift
---

You are performing a cross-department consistency review of the project vault.

Read ALL vault files:
1. `.claude/vault/00_Meta/project-brief.md`
2. `.claude/vault/00_Meta/execution-plan.md`
3. All department index files and their content files
4. The 3 most recent handoff notes

Check for:

1. **Contradictions:** Does any department file contradict another? (e.g., architecture says "microservices" but product requirements assume monolith)
2. **Stale content:** Do any vault files reference things that no longer exist in the codebase, or miss things that have been added?
3. **Orphaned decisions:** Are there decisions in `decisions/` folders that aren't reflected in the relevant content files?
4. **Plan drift:** Does the execution plan accurately reflect the actual state of the project, or has work been done without updating it?
5. **Missing context:** Are there areas of the codebase that the vault doesn't cover at all?
6. **Broken links:** Do all wikilinks resolve to actual vault files?

Present:

**Vault Health Report**

**Contradictions Found:** (list, or "None")
**Stale Content:** (list files that need updating)
**Orphaned Decisions:** (list, or "None")
**Plan Drift:** (description of any discrepancies)
**Coverage Gaps:** (areas of the project not represented in vault)
**Broken Links:** (list, or "None")

**Recommended Fixes:** (prioritised list of vault updates needed)

Do NOT make changes automatically. Present findings and let the user decide what to fix.
