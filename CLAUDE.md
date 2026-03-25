# Project Vault

This project uses a persistent vault at `.claude/vault/` to maintain context across sessions. The vault stores project knowledge (architecture, decisions, patterns, conventions) and tracks work (tasks, handoffs, execution plans).

**This plugin provides project memory and task management. It delegates all implementation work to OMC agents, enriching them with vault context.**

## Available Commands

| Command | When to use | Auto-run? |
|---------|-------------|-----------|
| `/resume` | START of every session. Always run this first. | Yes — run proactively |
| `/status` | When you need to check project state. | Yes — run proactively |
| `/review` | Periodically. Checks vault consistency and staleness. | Yes — run proactively |
| `/wrap-up` | END of every session. Always run before ending. | No — suggest to user |
| `/decide` | When a significant technical or product decision is made. | No — suggest to user |
| `/plan` | When new work needs to be broken into tasks or priorities change. | No — suggest to user |
| `/execute` | When ready to start building. Manages tasks and delegates to OMC agents. | No — suggest to user |
| `/compress` | When handoffs/ has 10+ files. Archives old handoffs. | No — suggest to user |
| `/populate` | First time only. Scans codebase and builds vault from scratch. | No — suggest to user |
| `/agent` | When spawning a specialist with vault context via OMC agents. | No — suggest to user |
| `/refine-skills` | After accumulating skill run data. Improves skills based on execution feedback. | No — suggest to user |
| `/software-req` | When generating Airtasker software requisition documents from vendor quotes. | No — suggest to user |
| `/blog` | When transforming research output into blog posts. | No — suggest to user |
| `/verified-research` | When doing structured live-web research with verification. | No — suggest to user |

## Integration with OMC

This plugin works alongside oh-my-claudecode. The boundary is:

| Concern | Owner |
|---------|-------|
| **What to work on** (tasks, priorities, dependencies) | Vault (this plugin) |
| **How to work on it** (agent selection, parallelism, delegation) | OMC |
| **What we know** (architecture, patterns, decisions, context) | Vault (this plugin) |
| **Who does the work** (executor, architect, designer, etc.) | OMC agents |
| **Session continuity** (handoffs, resume, status) | Vault (this plugin) |

When `/execute` runs a task, it:
1. Reads vault context (task details, architecture, patterns, conventions, persona guidance)
2. Delegates to the appropriate OMC agent with that context bundled into the prompt
3. Tracks completion and updates the vault

When OMC agents are used directly (outside `/execute`), the vault still provides context. Always check vault files for project-specific patterns and conventions before making changes.

## Auto-Invocation Rules

| Condition | Action |
|-----------|--------|
| Session start + `.claude/vault/` exists | Run `/resume` proactively |
| Vault exists + periodic check needed | Run `/status` or `/review` proactively |
| User says "wrap up", "end session", "done for today" | Suggest `/wrap-up` |
| User says "populate", "init vault", "bootstrap vault" | Invoke `/populate` |
| User says "software req", "vendor quote", "requisition" | Invoke `/software-req` |
| User says "write a blog post", "blog this" | Invoke `/blog` |
| User says "research", "investigate", "deep research" | Invoke `/verified-research` |
| Significant decision made during work | Suggest `/decide` |
| 10+ handoff files exist | Suggest `/compress` |

## Clarification Protocol

Before any write command that plans or builds work (`/plan`, `/execute`, `/populate`), you MUST reach at least 95% confidence that you understand what the user wants. Follow this interactive process:

**Step 1: Listen and reflect.** Restate your understanding in your own words. Be specific.

**Step 2: Identify gaps.** Ask questions one topic at a time. Most important first.

**Step 3: Confirm assumptions.** State assumptions explicitly and ask if correct.

**Step 4: Confidence check.**
- Below 80%: "I still have significant gaps."
- 80-95%: "I'm unsure about [specific thing]. Can you clarify?"
- 95%+: "Here's my final understanding: [summary]. Should I proceed?"

**Step 5: Suggestions round.** Consider what the user didn't ask for. Present in three tiers: things you probably need, things worth considering, ideas for later. User picks. Rejected items are dropped.

**Step 6: Final summary.** User confirms or corrects. Only then proceed.

## Command Execution Rules

**Read-only commands (`/resume`, `/status`, `/review`):** Run proactively without asking. They do not modify any files.

**Write commands (`/wrap-up`, `/decide`, `/plan`, `/compress`, `/populate`, `/agent`, `/refine-skills`):** Must NOT run without user confirmation. Suggest them clearly and wait for confirmation.

## Vault Rules

- **Do not modify vault files directly** outside of commands. Use the commands — they maintain consistency.
- **If you make a significant decision**, suggest running `/decide` to the user before continuing.
- **If you notice vault drift**, run `/review` yourself (read-only) and present findings.
- The vault is the source of truth for project decisions and state. Treat it as authoritative.
- **When delegating to OMC agents**, always include relevant vault context (patterns, conventions, architecture) in the agent prompt so the work aligns with project standards.
