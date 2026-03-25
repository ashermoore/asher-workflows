---
name: populate
description: First-time vault population - deeply reads codebase and creates comprehensive vault
---

You are populating the project vault from an existing codebase. This project has no vault yet (or the vault is empty). Your job is to deeply read the project and create a comprehensive vault that captures its current state.

CRITICAL RULES — follow these throughout every step:

1. **Source attribution is mandatory.** Every factual claim you write in a vault file MUST include a source reference in parentheses: the file path and line number (where practical) that supports the claim. Example:
   - "Uses Express.js v4.18.2 (from `package.json:15`)" [observed]
   - "Auth middleware validates JWT tokens (from `src/middleware/auth.ts:23-45`)" [observed]
   If you cannot cite a specific file for a claim, you must tag it `[inferred]` or `[unknown]`.

2. **Confidence tagging is mandatory.** Every statement in a vault file must end with one of these tags:
   - `[observed]` — you directly read this in a specific file. You can point to the exact source.
   - `[inferred]` — a reasonable conclusion from what you observed, but not explicitly stated anywhere. Example: "Appears to be a multi-tenant system based on tenant_id columns across all tables" [inferred]
   - `[unknown]` — you could not determine this from the codebase. Needs human input. Example: "Deployment target and hosting provider" [unknown]

3. **Never explain WHY unless evidence exists.** You may write WHAT exists ("The project uses PostgreSQL" [observed]) but you must NEVER write WHY a choice was made ("PostgreSQL was chosen for its JSONB support") UNLESS a README, comment, ADR, commit message, or other document explicitly states the reason. If no reason is documented, write: "Reason for this choice is not documented" [unknown].

4. **When in doubt, leave it out.** It is better to write "[unknown] — needs human input" than to guess. An incomplete vault that's accurate is far more valuable than a complete vault with hallucinations.

Follow these steps IN ORDER. Complete each step fully before moving to the next. Do not skip steps.

## Step 1: Project Survey

Read and analyse the following (skip any that don't exist):
- README.md or README (project description, setup instructions)
- package.json, Cargo.toml, go.mod, pyproject.toml, Gemfile, build.gradle, pom.xml (tech stack, dependencies)
- Directory listing of the project root and key subdirectories (understand the file structure)
- .github/workflows/ or CI config (build and deployment setup)
- Dockerfile, docker-compose.yml, infrastructure/ (infrastructure setup)
- CLAUDE.md, AGENTS.md (existing AI context if any)

List what you found and what the project appears to be. Cite every file you read.

## Step 2: Deep Read

Based on Step 1, identify and read the most important source files to understand:
- The system architecture (entry points, main modules, routing, data models)
- Key patterns and conventions (naming, structure, error handling)
- External dependencies and integrations

Read at least 10-15 key files. Prioritise entry points, configuration, and core business logic.

**Keep a running source log** — a list of every file you read and what you learned from it. You will need this for attribution in later steps.

## Step 3: Git History (if available)

Run `git log --oneline -50` to understand recent development history.
Run `git log --all --oneline --graph -20` to understand branching.
Identify: what has been built recently, what appears to be in progress, any patterns in commit messages.

Note: git history tells you WHAT happened and WHEN. It does NOT reliably tell you WHY. Do not infer motivation from commit messages unless they explicitly state it.

## Step 4: Create Vault Structure

Create the following folder structure:
```
.claude/vault/
  00_Meta/
    execution-plan.md
    project-brief.md
    tasks/
    handoffs/
    personas/
  01_RnD/
    index.md
    architecture.md
    tech-stack.md
    decisions/
  02_Product/
    index.md
    requirements.md
  03_Engineering/
    index.md
    patterns.md
    conventions.md
    skills/
      eval/
      skill-runs.md
```

Only create 04_Marketing/ and 05_Operations/ if the project has marketing content or significant deployment infrastructure.

Copy the persona files from the plugin's templates directory into `00_Meta/personas/`.

## Step 5: Write Project Brief

Write `.claude/vault/00_Meta/project-brief.md` following the project brief schema. Every line must have a source attribution and confidence tag. If the README doesn't explain the problem or solution clearly, write `[unknown]` and move on.

## Step 6: Write Department Files (First Pass)

For each department, write the content files. Remember: source attribution and confidence tags on EVERY claim.

**01_RnD:**
- `architecture.md` — document every component you identified, its purpose, technology, and location in the repo. Each component entry must cite the files that define it. Include data flow only if you can trace it through actual code paths (cite the files). If the data flow is unclear, write "[unknown] — data flow could not be fully traced" rather than guessing.
- `tech-stack.md` — list all technologies, frameworks, and key libraries with their EXACT versions from lockfiles/manifests. Cite the manifest file and line for each. Do not guess versions.
- `index.md` — navigation links to the above files.

**02_Product:**
- `requirements.md` — document what the product DOES based on code (routes, UI components, API endpoints, CLI commands). Tag everything as `[observed]` or `[inferred]`. Do NOT write aspirational requirements or guess at intended features. If a feature appears half-built, say so: "Appears partially implemented — only the API endpoint exists, no UI" [inferred].
- `index.md` — navigation links.

**03_Engineering:**
- `patterns.md` — document code patterns you observed. Each pattern must cite at least 2 examples from the codebase showing the pattern in use. A pattern seen in only one file is not a pattern — it's a single instance. Don't document it as established.
- `conventions.md` — document coding conventions. Cite specific files showing each convention. If conventions are inconsistent (some files use one style, others use another), note the inconsistency rather than picking one as "the" convention.
- `index.md` — navigation links.

## Step 7: Verification Pass (Second Pass)

This is the anti-hallucination gate. For EVERY vault file you created in Steps 5-6:

1. Re-read the vault file.
2. For each claim tagged `[observed]`, re-read the cited source file and confirm the claim is accurate. If you cannot confirm it, downgrade to `[inferred]` or remove it.
3. For each claim tagged `[inferred]`, assess whether the inference is reasonable and clearly stated as an inference. If it's a stretch, downgrade to `[unknown]`.
4. Check that no claims lack a confidence tag. If any do, add one.
5. Check that no `[observed]` claims lack a source citation. If any do, either find the source or downgrade the tag.

Report how many claims were downgraded or removed during verification.

## Step 8: Create Initial Execution Plan

Based on git history and code analysis, create `.claude/vault/00_Meta/execution-plan.md`:
- Identify work that appears complete (mark as "completed") — cite the evidence
- Identify work that appears in progress (mark as "in-progress") — cite the evidence
- Identify obvious gaps or next steps (mark as "not-started") — but ONLY if clearly evidenced by TODOs, issues, or incomplete code. Do not invent a roadmap.
- If you cannot determine the project's roadmap, create a minimal plan with only what's evident and write: "Execution plan is minimal — project roadmap could not be determined from codebase. Needs human input to define upcoming work." [unknown]

## Step 9: Write Session-Zero Handoff

Create `.claude/vault/00_Meta/handoffs/000-YYYY-MM-DD.md` as a "session zero" handoff:
- Document that this was an automated vault population
- List all vault files created
- Summarise the project state as understood
- List ALL `[unknown]` items across all vault files — these are the things the user needs to clarify
- List ALL `[inferred]` items that you're least confident about — flag these for human review
- Recommend what the first real work session should focus on

## Step 10: Human Review Checkpoint

Do NOT consider the vault populated until the user has reviewed it. Present a structured review:

**Vault Population Complete — Awaiting Human Review**

**Confidence Summary:**
- [observed] claims: N (these are backed by specific source files)
- [inferred] claims: N (these are reasonable but unverified — please review)
- [unknown] items: N (these need your input)

**Items Needing Your Review:**

1. **[unknown] items** (things I couldn't determine):
   - (list each one with context about what information is needed)

2. **[inferred] items I'm least confident about:**
   - (list the 5-10 inferences you're least sure of, with the reasoning)

3. **Department-by-department summary:**
   - **RnD:** N claims (N observed, N inferred, N unknown)
   - **Product:** N claims (N observed, N inferred, N unknown)
   - **Engineering:** N claims (N observed, N inferred, N unknown)

**Next step:** Please review the items above. For each `[unknown]`, either provide the answer or confirm it should stay unknown. For each `[inferred]` item, confirm or correct it. Once reviewed, the `[inferred]` tags on confirmed items can be upgraded to `[confirmed]` and the vault becomes the source of truth.

**After review:** Run `/resume` to begin your first tracked session.
