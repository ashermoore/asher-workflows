---
name: agent
description: Load vault context for a department and delegate to the appropriate OMC agent
---

You are configuring a specialist agent for a specific department. The user will specify which department (or you should infer from context).

For the requested department, do two things:
1. **Load vault context** — read the persona file and department vault files for project-specific knowledge
2. **Delegate to the right OMC agent** — pass the vault context as enrichment to the OMC agent that will do the actual work

## Department Configuration

### RnD
- **Vault context to load:**
  - Persona: `.claude/vault/00_Meta/personas/rnd-architect.md`
  - Files: `01_RnD/index.md`, `01_RnD/architecture.md`, `01_RnD/tech-stack.md`, all files in `01_RnD/decisions/`
- **Delegate to:** `oh-my-claudecode:architect` (opus) for analysis/decisions, `oh-my-claudecode:executor-high` (opus) for implementation

### Product
- **Vault context to load:**
  - Persona: `.claude/vault/00_Meta/personas/product-manager.md`
  - Files: `02_Product/index.md`, `02_Product/requirements.md`, `02_Product/user-stories.md` (if exists)
- **Delegate to:** `oh-my-claudecode:analyst` (opus) for analysis, `oh-my-claudecode:planner` (opus) for planning

### Engineering
- **Vault context to load:**
  - Persona: `.claude/vault/00_Meta/personas/senior-engineer.md`
  - Files: `03_Engineering/index.md`, `03_Engineering/patterns.md`, `03_Engineering/conventions.md`, `03_Engineering/tech-debt.md`
- **Delegate to:** `oh-my-claudecode:executor` (sonnet) for standard work, `oh-my-claudecode:executor-high` (opus) for complex work

### Operations
- **Vault context to load:**
  - Persona: `.claude/vault/00_Meta/personas/ops-lead.md`
  - Files: `05_Operations/index.md` and all files in the operations department (if exists)
- **Delegate to:** `oh-my-claudecode:executor` (sonnet) for implementation, `oh-my-claudecode:architect` (opus) for infrastructure decisions

### Design
- **Vault context to load:**
  - Persona: `.claude/vault/00_Meta/personas/ui-designer.md`
  - Files: `02_Product/index.md`, `02_Product/requirements.md`, `02_Product/design-system.md` (if exists), `03_Engineering/patterns.md`, `03_Engineering/conventions.md`
- **Delegate to:** `oh-my-claudecode:designer` (sonnet) for standard UI, `oh-my-claudecode:designer-high` (opus) for complex design systems

### Security
- **Vault context to load:**
  - Persona: `.claude/vault/00_Meta/personas/security-reviewer.md`
  - Files: `01_RnD/architecture.md`, `03_Engineering/security-audit.md` (if exists), `03_Engineering/threat-model.md` (if exists), `03_Engineering/patterns.md`, `01_RnD/tech-stack.md`
- **Delegate to:** `oh-my-claudecode:security-reviewer` (opus)

### Test
- **Vault context to load:**
  - Persona: `.claude/vault/00_Meta/personas/test-engineer.md`
  - Files: `03_Engineering/index.md`, `03_Engineering/patterns.md`, `03_Engineering/conventions.md`, `03_Engineering/test-strategy.md` (if exists), `01_RnD/tech-stack.md`
- **Delegate to:** `oh-my-claudecode:tdd-guide` (sonnet) for test writing, `oh-my-claudecode:qa-tester` (sonnet) for interactive testing

### Code Review
- **Vault context to load:**
  - Persona: `.claude/vault/00_Meta/personas/staff-engineer.md`
  - Files: `01_RnD/architecture.md`, `01_RnD/decisions/` (all), `03_Engineering/patterns.md`, `03_Engineering/conventions.md`, `03_Engineering/tech-debt.md` (if exists)
- **Delegate to:** `oh-my-claudecode:code-reviewer` (opus)

## For ALL departments, also load:
- `.claude/vault/00_Meta/execution-plan.md` to understand current project state
- The latest handoff note in `.claude/vault/00_Meta/handoffs/` for recent context

## How to delegate

When spawning the OMC agent, include the vault context in the prompt:

```
TASK: [user's request]

PROJECT CONTEXT (from vault):
- Architecture: [summary from architecture.md]
- Patterns: [relevant patterns from patterns.md]
- Conventions: [relevant conventions from conventions.md]
- Current state: [summary from execution plan]
- Recent activity: [summary from latest handoff]

DEPARTMENT GUIDANCE (from [persona].md):
- Priorities: [top priorities from persona]
- Review criteria: [relevant criteria]
- Anti-patterns to avoid: [relevant anti-patterns]
- Communication style: [from persona]

[user's specific instructions]
```

After delegating, confirm:

**Agent Configured: [Role Title] ([Department Name])**
**Vault Context Loaded:** (list vault files read)
**Delegated To:** (OMC agent name and model)
**Current Relevant Tasks:** (list tasks from execution plan assigned to this department)

Then let the OMC agent proceed with the work.
