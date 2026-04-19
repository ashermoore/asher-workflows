---
name: confluence-publishing
description: Publish, update, and maintain Confluence pages via the Atlassian MCP server. Use whenever the user mentions Confluence, a Confluence page URL, a page title in a Confluence space, updating or editing an existing Confluence doc, addressing Confluence comments (inline or footer), cleaning up a draft before publishing, or porting Markdown/notes into Confluence. Also trigger for phrases like "publish this to the wiki", "update the runbook", "reply to the comments on that page", "turn these notes into a Confluence doc", or when the user shares a page that needs copy-edit, restructuring, or comment resolution. Confluence is a communication medium read by humans first and agents second, so this skill enforces a house style (no title repeat, no horizontal rules, no em dashes, British English, working @ mentions, visual panels, no repetition, logical flow) and a safe edit protocol that preserves manual edits and existing comments.
---

# Confluence Publishing

Confluence is a communication medium. Pages are read by humans first and agents second, so every change should leave the page tidier, more scannable, and more trustworthy than it was before.

This skill covers three workflows on Confluence Cloud via the Atlassian Rovo MCP tools:

1. **Create** a new page from notes, a draft, or a conversation.
2. **Edit** an existing page without trampling manual edits or orphaning inline comments.
3. **Address comments** (inline and footer) by making targeted edits and replying.

The section "House style checklist" applies to all three. It is non-negotiable: run through it before every write.

## Which MCP this assumes

This skill assumes the Atlassian Rovo MCP server (the canonical one Atlassian publishes at `atlassian/atlassian-mcp-server`) is connected. The tool names in the table below are the Confluence tools that server exposes. The `mcp__atlassian__` prefix below is the Claude Code naming convention; on claude.ai and other clients the namespace prefix may differ (for example, just `atlassian.*`), but the underlying tool names after the prefix are the same. Before the first call, if your client uses a different prefix, mentally substitute it; the names after the prefix are what you match on.

Before relying on any tool below, call it (or list available tools) and confirm the exact names your tenant exposes. If a tool is missing, stop and tell the user the Atlassian MCP is not connected, or that this specific capability is not exposed, rather than guessing at a REST endpoint.

## Tools you will use

| Purpose | Tool | Notes |
|---|---|---|
| Read a page (body + version) | `mcp__atlassian__getConfluencePage` | |
| Find a page by title / free text | `mcp__atlassian__searchConfluenceUsingCql` | |
| List pages in a space | `mcp__atlassian__getPagesInConfluenceSpace` | |
| List child pages / folders | `mcp__atlassian__getConfluencePageDescendants` | |
| List spaces the user can access | `mcp__atlassian__getConfluenceSpaces` | |
| Read inline comments | `mcp__atlassian__getConfluencePageInlineComments` | |
| Read footer (page-level) comments | `mcp__atlassian__getConfluencePageFooterComments` | |
| Add or reply to a footer comment | `mcp__atlassian__createConfluenceFooterComment` | Safe default for any reply you want to leave. |
| Reply to an existing inline comment thread | `mcp__atlassian__createConfluenceInlineComment` | Tenant-dependent. If exposed, pass the parent inline comment id and inherit the anchor. Creating a *new* inline anchor from scratch is not reliably supported from an agent context; when the tool is absent or the parameters are unclear, fall back to a footer comment that quotes the anchor text and references the inline comment id. |
| Create a new page | `mcp__atlassian__createConfluencePage` | |
| Update an existing page | `mcp__atlassian__updateConfluencePage` | Replaces the full body; see Workflow 2. |

There is currently no Rovo MCP tool for resolving comments. When you have addressed a comment, say so in the reply and invite the commenter to resolve; do not claim the thread is closed.

Content is exchanged with these tools as Markdown or Atlassian Document Format depending on the parameter. See `references/storage-format.md` for the gotchas (panels, @mentions, tables, code blocks, status badges).

## Pre-flight: always do this first

Before writing, updating, or replying, gather context. Skipping this step is the single most common way to produce a bad Confluence edit.

1. **Confirm the target.** You need the page's `spaceKey` (for new pages) or the page `id` (for existing pages). If the user gave a URL, extract the numeric id. If they named a page, call `searchConfluenceUsingCql` to find it, then confirm with the user which result is the right one when there is any ambiguity.
2. **Read what is already there.** For edits and comment work, call `getConfluencePage` and capture the current `version.number`, the title, and the full body. You will need the version number to update.
3. **Read the comments.** Call `getConfluencePageInlineComments` and `getConfluencePageFooterComments` even when the user only asked for an edit. Inline comments anchor to specific text, and a careless edit will orphan them. You need to know what is there before you write.
4. **Surface a plan.** Before any write, describe what you are going to do in plain language: which sections you will change, which you will leave, which comments you will resolve, what you will reply to. Wait for approval. Never batch-apply without a confirmation step.

Use this confirmation as your chance to flag anything that looks like a manual edit the user cares about (a hand-typed note, a recently-updated table, a block with tracked changes). Ask before overwriting it.

## Workflow 1: Create a new page

1. **Clarify the essentials** if the user has not already told you:
   - Space key and parent page (if any)
   - Audience (who reads this, and what they need to do after reading)
   - Whether this supersedes an existing page
2. **Draft the body in Markdown**, following the house style checklist below.
3. **Preview with the user.** Paste the full draft body into the chat inside a fenced ```` ```markdown ```` block so they can read it as it will render, alongside a short three-to-five-line outline of the section structure ("Purpose, Context, Rollout plan, Owners, Action required"). Call out any placeholders, guessed @mentions, or unconfirmed facts explicitly. Ask: "Happy for me to publish this as-is, or anything to change?" Wait for approval.
4. **Publish** with `createConfluencePage`. Capture the returned URL and share it.
5. **Post-publish sanity check.** Re-read the page with `getConfluencePage` and confirm panels rendered, @mentions resolved, and code blocks did not get mangled. Fix with a follow-up `updateConfluencePage` if needed.

## Workflow 2: Edit an existing page

This is the highest-risk workflow. The user may have typed something directly into Confluence that is not in your chat history. Treat the live page as the source of truth.

1. **Pre-flight** as described above. You must have the current body, version number, and comments in hand.
2. **Diff in your head.** What is on the page? What did the user ask you to change? Everything else stays.
3. **Preserve inline-commented ranges.** If an inline comment is anchored to a sentence, avoid rewriting that sentence unless the user specifically asked you to. If you must rewrite it, plan to leave a footer comment explaining what happened to the anchor.
4. **Show the plan.** Present a short table to the user with three columns: section, what's there now, what you'll do (keep verbatim, replace with X, add new section Y, etc.). Below the table, list any inline comments whose anchor text will change and any comments you will reply to. Get approval before writing.
5. **Update** with `updateConfluencePage`, passing the current `version.number` plus 1. The whole body is replaced on every update, so the body you send must include the parts you intend to keep, verbatim.
6. **Verify.** Re-read the page. Confirm nothing important was dropped and formatting still renders.

### Trivial-edit carve-out

For a fully-specified one-token change the user has already diagnosed, the full plan-and-approve protocol is overkill and trains people to stop using the skill. Examples of trivial edits:

- Fix a typo the user pointed at ("change 'recieve' to 'receive' in the Rollback section")
- Update a stale dead link to the new URL the user gave you
- Increment a version number or date the user dictated

For these cases, the protocol collapses to: pre-flight read, state the exact change in one line ("Changing 'recieve' to 'receive' in Rollback, no other edits"), apply, verify. No approval round needed because the user already approved by giving you the exact change. The pre-flight read is still required, because the live page may contain a manual edit elsewhere that the change interacts with.

If the change is anything more interpretive than this ("update the rollback section" without specifying how), it is not trivial; use the full protocol.

Detailed guidance, including how to reason about conflicting manual edits, is in `references/editing-existing.md`.

## Workflow 3: Address comments

Addressing comments well is part editorial work, part communication. A comment is a request from a human, and silence is the wrong answer.

1. **Collect all open comments** via both `getConfluencePageInlineComments` and `getConfluencePageFooterComments`. Note comment ids, the anchor text (for inline), the author, the ask, and the age.
2. **Classify each comment** into one of:
   - **Actionable edit** – comment asks for a specific change to the page
   - **Question** – comment asks something answerable without editing the page
   - **Suggestion** – comment proposes an approach, needs judgement
   - **FYI / resolved by context** – no action required
3. **Propose a response plan** for each comment: what edit (if any), what reply text, whether to resolve. Wait for approval.
4. **Make the page edits first** (Workflow 2), then **reply to each comment** via the appropriate `createConfluence…Comment` tool, referencing what changed. Close the loop: readers should not have to hunt to see how their feedback landed.
5. **Resolve** comments only if the Atlassian MCP exposes a resolve action; otherwise mention in the reply that the change has been made and the commenter can resolve.

See `references/comments-workflow.md` for reply templates and worked examples.

## House style checklist

Run through this list *before every* `createConfluencePage` and `updateConfluencePage` call. It exists because these mistakes keep happening and each one erodes the page's credibility.

**No title repeat.** Confluence renders the title at the top of the page already. Do not start the body with `# <Title>` or a heading that duplicates it. Start with the first section of real content.

**No horizontal rules.** Do not use `---`, `***`, or `<hr>` to separate sections. Use headings (`##`, `###`) and whitespace. Horizontal rules render as thin lines in Confluence and add visual noise without structure.

**No em dashes.** Replace the em dash character (`\u2014`, the long dash) with a comma, a colon, a full stop, or restructure the sentence. En dashes (`\u2013`) for number ranges are fine. Hyphens (`-`) in compound words are fine. This rule is about the long dash specifically.

**British English.** Default to British spellings: _organise, organised, organisation, colour, behaviour, flavour, favour, realise, recognise, prioritise, analyse, optimise, centre, theatre, metre, litre, grey, travelled, cancelled, licence (noun), practise (verb), defence._ Run a search for common American forms (`-ize`, `-ization`, `color`, `behavior`, `center`, `analyze`, `optimize`, `gray`) before publishing.

**Working @ mentions.** If a table or paragraph lists people by username, use actual Confluence `@mention` markup so the names resolve to profile links. Plain text `@asher` is not a mention. If you do not know the user's account id or username, ask, or fall back to their full name without the `@`.

**Visual callouts.** Use Confluence info / note / warning / success panels to draw attention to important caveats, next steps, or dependencies. A wall of plain prose is hard to scan. Reach for a panel whenever you catch yourself writing "Note that…", "Be aware…", "Important:…", or "Action required:".

**No repetition.** Read the full page top to bottom before publishing. If a point is made twice, cut one copy. If the conclusion restates the introduction, trim it to a one-line summary or cut it entirely.

**Logical flow.** A reader who knows nothing about the topic should follow the page top-to-bottom without jumping back. Lead with context (why this exists), then the substance, then the calls to action. If you catch yourself writing "as mentioned above", the structure is probably wrong, consolidate the point in one place.

**Respect existing comments.** Before overwriting a paragraph, check whether an inline comment is anchored to it. If yes, and the change is not what the commenter asked for, either leave the anchor text intact or add a footer comment explaining the rewrite.

**Respect manual edits.** The user may have typed directly into Confluence. Never silently overwrite content you did not produce. When the plan would replace text you did not write, flag it and ask.

The full checklist with worked "before/after" examples lives in `references/style-checklist.md`. Read it any time you are unsure.

## Common pitfalls

- **Stale version number.** If you read the page, wait, then update, someone may have edited in between. If the update fails with a conflict, re-read with `getConfluencePage` and redo the merge, do not blindly retry with the next integer.
- **Markdown that does not render.** Confluence accepts Markdown but has quirks around tables inside callouts, nested lists, and raw HTML. If a construct does not render, fall back to the equivalent wiki markup, see `references/storage-format.md`.
- **Orphaned inline comments.** Inline comments anchor to specific text. Rewriting that text makes the comment dangle. Re-read inline comments before editing and plan around them.
- **Too many panels.** Panels work because they are rare. A page where every second block is a panel is no easier to scan than a page with none.
- **"Just one more edit".** Batch changes. Every `updateConfluencePage` triggers notifications to watchers. Plan the whole edit, apply once.

## Not covered in v1

The following are deliberately out of scope for this version of the skill. If the user asks for them, do your best with the underlying MCP tools and warn that the skill does not have specific guidance:

- **Page labels** (adding, removing, listing). The Rovo MCP exposes `add label` and `get labels` tools; this skill does not give house rules for labelling.
- **Page restrictions** (view/edit ACLs). Not addressed; ask the user if they want any specific restriction set after publish.
- **Parent-page moves and reparenting.** The MCP supports `moveConfluencePage`-style operations on some tenants; treat moves as a separate request, not part of an edit.
- **Attachments and image upload.** Inline image references work if the attachment is already on the page; uploading new attachments may require a separate flow.
- **Templates and blueprint-based pages.** Always renders as a blank page from a Markdown body.

## Reference files

- `references/style-checklist.md`, full house style rules with before/after examples
- `references/storage-format.md`, Markdown → Confluence gotchas (panels, mentions, tables, code, status)
- `references/editing-existing.md`, detailed protocol for safe edits with manual-edit detection
- `references/comments-workflow.md`, addressing inline and footer comments, reply templates
