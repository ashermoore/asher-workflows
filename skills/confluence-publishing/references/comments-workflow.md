# Comments Workflow: Inline and Footer

Comments on a Confluence page are a low-friction way for colleagues to leave feedback without blocking an edit. When a user asks you to "address the comments on that page", the expected behaviour is to read every open comment, decide what each one needs, make the appropriate edit (if any), and leave a reply so the commenter knows what happened.

Silence is the wrong answer. A comment that gets an edit but no reply looks ignored.

## Two kinds of comments

**Inline comments** are anchored to a specific span of text in the page body. They show up as highlighted text with a little marker. They are fetched with:

```text
mcp__atlassian__getConfluencePageInlineComments({ pageId })
```

**Footer comments** (sometimes called page-level comments) live at the bottom of the page and are not tied to specific text. They are fetched with:

```text
mcp__atlassian__getConfluencePageFooterComments({ pageId })
```

Both kinds can have threaded replies. Gather the whole thread for each comment before deciding how to respond, because the user's question on reply 3 may be what actually needs answering, not the top-level comment.

## Full workflow

### 1. Collect

Read both the inline and footer comment streams. Build a table:

| id | author | type | anchor (if inline) | age | status | ask |
|---|---|---|---|---|---|---|
| 101 | @priya | inline | "rollback takes 15 minutes" | 3 days | open | "this is now 30" |
| 102 | @tom | inline | "@alice owns rollback" | 1 day | open | "Alice is on PTO, who is covering?" |
| 103 | @priya | footer | (n/a) | 2 hours | open | "Can we add a section on monitoring?" |

Include already-resolved comments in your initial read so you can see context, but you will only act on open ones.

### 2. Classify

For each open comment, pick one of:

- **Actionable edit**, the comment asks for a specific change to the body. Plan an edit.
- **Question**, the comment asks something answerable without editing the body. Plan a reply only.
- **Suggestion**, the comment proposes an approach or change and needs judgement. Plan a reply proposing the approach you want to take, and wait for the commenter if needed.
- **FYI / resolved by context**, no action needed. Reply acknowledging and, if possible, resolve.

### 3. Plan

Propose, per comment, the reply text and any associated page edit. Example:

| id | edit | reply |
|---|---|---|
| 101 | Change "15 minutes" to "30 minutes" in Rollback section | "Updated to 30 minutes, thanks for catching." |
| 102 | No edit | "Alice is on PTO this week; @mark.li is covering rollbacks until she's back." |
| 103 | Add new "Monitoring" section with the three dashboards and the on-call playbook link | "Added a Monitoring section with the dashboards and the playbook link. Let me know if anything's missing." |

Present this plan to the user. Wait for approval before writing.

### 4. Make page edits first, then reply

Edit the page once with all of the combined changes (see `editing-existing.md`), using `updateConfluencePage`. Do **not** reply to comments before the edit lands, if the edit fails, your reply will be wrong.

Once the update succeeds, reply to each comment.

**Footer comment replies** are the safe default. Every Rovo MCP tenant exposes `mcp__atlassian__createConfluenceFooterComment`, and replies to existing footer threads work via `parentCommentId`:

```text
mcp__atlassian__createConfluenceFooterComment({
  pageId: "12345",
  parentCommentId: "103",
  body: "Added a Monitoring section..."
})
```

**Inline comment replies** depend on what your tenant exposes. If `mcp__atlassian__createConfluenceInlineComment` is available and accepts `parentCommentId`, use it for replies, the reply inherits the anchor of the parent inline comment, you don't need to specify a new range:

```text
mcp__atlassian__createConfluenceInlineComment({
  pageId: "12345",
  parentCommentId: "101",
  body: "Updated to 30 minutes, thanks for catching."
})
```

If that tool is missing in your tenant, or its parameters demand an anchor range you can't reliably construct, fall back to a footer comment that quotes the inline anchor and references the comment id, so the commenter can find it:

> Re: inline comment 101 ("rollback takes 15 minutes"), updated to 30 minutes in the Rollback section.

Creating a *new* inline comment from scratch with a fresh anchor is rarely the right move from an agent context, you'd need to reference an exact span of text in the page body and the API support is uneven. If a user explicitly asks for a new inline anchor, ask them to add it directly in Confluence.

The exact parameter names are MCP-dependent; check the tool's input schema before the first call.

### 5. Resolve

If the MCP supports resolving comments, resolve the ones you have fully addressed. If it does not, mention in the reply that the change has been made and invite the commenter to resolve. Do not leave "done" replies on open threads that linger forever.

## Reply templates

Short, specific, grateful. Don't explain more than needed.

**Addressed with an edit:**

> Updated, changed "15 minutes" to "30 minutes" in the Rollback section.

**Question answered without an edit:**

> Alice is on PTO this week; @mark.li is covering rollbacks until she's back.

**Suggestion you're taking:**

> Good idea. Added a Monitoring section with the three dashboards and the on-call playbook link. Let me know if anything's missing.

**Suggestion you're declining:**

> Thought about it, I'd rather keep the rollback section short and link out to the detailed playbook, because readers here are usually triaging an incident. Open to revisiting if that's not working.

**Out of scope:**

> This is a good point but bigger than this page, created a follow-up task: [link]. I'll leave this unresolved so we can come back to it.

## Inline comment anchors

Inline comments anchor to a specific range of text. If your edit removes or substantially rewrites that text, the comment becomes "dangling": the highlight disappears and the comment's context is lost.

Rules:

1. **If the comment is asking you to change that exact text**, change it, the commenter wants it gone. The comment will dangle after, which is fine; your reply will explain.
2. **If the comment is asking about the text but you're changing it for unrelated reasons**, preserve as much of the anchor phrase as you can. If you can't, add a footer comment explaining what the edit was so the context isn't lost.
3. **If the comment is unrelated to a rewrite you're doing**, preserve the anchor text verbatim.

## Worked example

**Situation.** The user says "Please clear the open comments on the Q2 runbook."

**Steps:**

1. Fetch the page and both comment streams. Find five open comments (two inline, three footer).
2. Classify: three actionable edits, one question, one FYI.
3. Draft the plan table. Present to the user.
4. User approves with a tweak ("don't add the monitoring section yet, we're still deciding").
5. Update the plan, make the page edits for the other two actionable comments.
6. `updateConfluencePage` once.
7. Verify the page rendered correctly and the inline anchors that should still be there are still highlighted.
8. Reply to each of the five comments with short, specific messages. For the monitoring suggestion, reply: "Holding on this one per @asher, we're still deciding the approach. Leaving unresolved."
9. Tell the user: five comments addressed (two edited + replied, one answered in thread, one held, one FYI acknowledged).

## Things that go wrong

- **Replying before the edit lands.** Your reply says "fixed", but the edit failed a version check and did not go through. Always edit first, verify, then reply.
- **Over-replying.** A one-word "done" on every comment is less useful than a specific reply that says what changed.
- **Answering for the user.** If a comment asks something you don't actually know ("is this still Alice's area?"), ask the user in chat rather than guessing.
- **Ignoring older threads.** Sometimes comments from six months ago are still open because they were never resolved. Read them. If the answer is "no longer relevant", reply saying so and resolve. Don't leave ancient threads littering the page.
