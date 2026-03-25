---
description: Refine vault skills based on accumulated execution data using one-change-at-a-time methodology
---

You are refining vault skills based on accumulated execution data. This follows the Karpathy auto-research principle: make ONE change at a time, measure the impact, keep improvements and revert regressions.

## Step 1: Assess Current State

1. Read `.claude/vault/03_Engineering/skills/skill-runs.md` for run data.
2. Read all skill files in `.claude/vault/03_Engineering/skills/` (excluding the eval/ subfolder and skill-runs.md).
3. Read any eval files in `.claude/vault/03_Engineering/skills/eval/` to understand the assertion criteria.

If skill-runs.md has no run data or doesn't exist, report "No skill runs logged yet. Skills need to be used in actual tasks before they can be refined. Run /execute or complete tasks using skills, then try again." and stop.

## Step 2: Identify Refinement Candidates

For each skill that has runs logged:
1. Calculate the current pass rate across all logged runs
2. Identify the most commonly failing assertions
3. Identify gotchas that were discovered but not yet incorporated into the skill steps
4. Check if any run entries mention steps that were "unclear", "missing", or "wrong"

Categorise each skill:
- **Perfect (100% pass rate, no pending gotchas):** No refinement needed.
- **Needs Refinement (<100% pass rate OR has unincorporated gotchas):** Candidate for improvement.
- **Untested (no eval file or no runs):** Cannot be refined automatically. Flag for manual review or eval creation.

## Step 3: Apply ONE Change Per Skill

For each skill that needs refinement, apply exactly ONE change. Choose the highest-impact change:

**Priority order for changes:**
1. **Incorporate a discovered gotcha** — if a gotcha was noted in a run entry but isn't in the skill's Gotchas section, add it. If the gotcha represents a missing step, add the step.
2. **Clarify an ambiguous step** — if an assertion consistently fails because a step is unclear, rewrite that step to be more specific (add exact file paths, exact commands, explicit instructions).
3. **Add a missing step** — if an assertion fails because the skill doesn't cover something it should, add a new step.
4. **Reorder steps** — if failures suggest the steps are in the wrong order, reorder them.

**Rules:**
- ONE change per skill per refinement run. Do not rewrite the entire skill.
- After making the change, increment the skill's Version number.
- Update the "Last Refined" date to today.
- Add a changelog entry: "vN — [what changed] (from session NNN, assertion AXXX)"
- Update the skill's pass rate based on the most recent data.

## Step 4: Present Results

**Skills Refined**

| Skill | Previous Pass Rate | Change Made | New Version |
|-------|-------------------|-------------|-------------|
| add-api-endpoint | 80% (4/5) | Added step 4b: update barrel export in routes/index.ts | v2 |

**Skills at 100%:** (list — no changes needed)
**Skills Untested:** (list — need eval files or runs before refinement)
**Skills with Pending Gotchas:** (gotchas discovered but not yet promoted to steps — will be addressed in next refinement run)

**Validation:** The changes above should be validated by running the affected skills on actual tasks. If a skill's pass rate improves after the change, the refinement was successful. If it drops, revert the change (restore the previous version from the changelog) and try a different approach in the next refinement run.

**Recommended next step:** Use the refined skills in upcoming tasks via /execute, then run /refine-skills again after accumulating new run data.
