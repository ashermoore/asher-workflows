# Editing Existing Confluence Pages

Editing a page someone else (or a past version of you) wrote is riskier than writing from scratch. The live page is the source of truth, not your chat history. This document describes the protocol to follow so you never silently overwrite a manual edit, orphan an inline comment, or lose content to a version conflict.

## The core idea

> The page you are about to update may have content you've never seen, added by a human between now and the last time you looked. The only safe assumption is that the body you fetched five seconds ago is the ground truth, and anything you want to keep must be in the body you send back.

Every update replaces the full body of the page. There is no partial update. This is why reading carefully before writing matters so much.

## Protocol

### 1. Read the current page

```text
mcp__atlassian__getConfluencePage({ pageId: "12345" })
```

Capture these fields:

- `title`, you may or may not be changing this
- `version.number`, you need this to update. The new update must send `version.number + 1`.
- `body`, the current Markdown / ADF content

Read the body in full. Do not skim.

### 2. Read all open comments

```text
mcp__atlassian__getConfluencePageInlineComments({ pageId: "12345" })
mcp__atlassian__getConfluencePageFooterComments({ pageId: "12345" })
```

For inline comments, note the anchor text (the span of the body the comment is attached to) and the ask. You will plan around these.

For footer comments, note any that are unresolved questions or requests directed at a future editor. They might be the reason the user is asking for this edit.

### 3. Identify manual edits

"Manual edit" means content on the page that did not come from you and is not something the user is asking you to change. Look for:

- Text that is not in your chat history and is not in the user's request
- Recent timestamps, tracked changes, or "TODO/FIXME" style notes
- Hand-written tables or lists whose rows do not match your draft
- Fresh-looking panels, badges, or callouts

Anything in this category is a candidate to preserve.

### 4. Build a plan

Write out the plan in three columns before touching the MCP:

| Section | What's there | What I'll do |
|---|---|---|
| Intro | Short purpose paragraph | Keep verbatim |
| Overview | Diagram + two paragraphs, mentions new Kafka pipeline | Keep. This is a manual edit I don't recognise. |
| Rollout plan | Bulleted steps | Replace with the user's updated list |
| Risks | Empty | Add the new section the user asked for |

List the inline comments separately:

- Comment on "the database migration runs in under 10 minutes" → edit to remove the 10-minute claim (we don't know this any more). Reply to comment noting the change.
- Comment on "@alice owns rollback" → anchor text is unchanged; no action on anchor. Reply "still accurate".

Present this plan to the user. Wait for approval.

### 5. Build the new body

Assemble the new body by taking the current body and applying only the changes in the plan. Copy-paste the sections you are keeping verbatim. Do not re-summarise them.

Run the house style checklist (`style-checklist.md`) against the new body. It is easy to forget the checklist when you are "only editing one section", don't. A mixed-style page (new section in American English, rest in British English) is worse than either one consistently.

### 6. Update

```text
mcp__atlassian__updateConfluencePage({
  pageId: "12345",
  title: "<same or updated>",
  body: "<new full body>",
  version: { number: <current + 1> }
})
```

If the update fails with a version conflict, the page was updated between your read and your write. Do not blindly retry with `version.number + 1`. Re-read the page with `getConfluencePage`, re-do the plan against the new body, and try again.

### 7. Verify

Re-read the page. Confirm:

- The sections you intended to keep are intact.
- Panels, tables, and code blocks render. (Confluence sometimes mangles Markdown you were confident about; see `storage-format.md`.)
- Inline comments still show against their intended anchors. If any look dangling, add a footer comment explaining the edit.

### 8. Reply to comments

If the edit addressed specific comments, go to `comments-workflow.md` and reply to each one, closing the loop for the commenter.

## When the user wants "just a small edit"

The protocol scales down. There are two flavours, depending on how fully specified the change is.

**Flavour 1: small but interpretive.** "Tighten the rollback section, it's too wordy." There's still a judgement call here, so:

1. Read the page.
2. Read inline comments (still, the thing you are about to change might be anchored).
3. Propose the rewrite: show the before/after of the affected section.
4. Get approval.
5. Update with the new version number.
6. Verify.

**Flavour 2: trivial, fully specified.** "Change 'recieve' to 'receive' in the Rollback section." The user has handed you the exact diff, so an approval round adds friction without adding safety:

1. Read the page (still, in case there's a manual edit nearby that the change interacts with).
2. State the change in one line: "Changing 'recieve' to 'receive' in Rollback, no other edits."
3. Apply with the new version number.
4. Verify.

The carve-out is for genuinely unambiguous one-token edits, fixed typos, dead-link replacements where the user gave you the new URL, version bumps the user dictated. If the change requires any interpretation, use Flavour 1. When in doubt, use Flavour 1; the cost of one extra round-trip is much smaller than the cost of one wrongly-overwritten paragraph.

Skipping the pre-flight read on either flavour saves you twenty seconds and costs you an overwritten paragraph about once a month. Not worth it.

## When the user pastes in a new draft

If the user provides a wholesale rewrite and asks you to "replace the page with this":

1. Read the current page anyway.
2. Identify manual edits, ownership tables, @ mentions, and open comments that are not in the new draft.
3. Flag them to the user: "Your draft doesn't include the ownership table, the warning panel about GDPR, or the inline comments from Priya. Do you want me to carry those over?"
4. Do not assume the user wants them dropped just because they were not in the draft.

## Conflict resolution heuristics

If two changes conflict, the user asks for X, but the live page already has a manual edit that implies not-X, pause and ask. Do not silently pick one. The user's chat message and the page's current state are two sources of intent; reconciling them is a decision, not an implementation detail.

## Worked example

**Situation.** The user says "Update the Q2 runbook with the new rollback steps I'm about to paste."

**Steps:**

1. Read the page. Note the current body has a section "Rollback" with three steps and a "Last reviewed: 2026-03-12" note that was not in your previous draft of this page.
2. Read inline comments. There is one on the phrase "rollback takes 15 minutes" saying "this is now 30, please update".
3. Build the plan: replace the three rollback steps with the user's new five steps; keep the "Last reviewed" note but update the date; change "15 minutes" to "30 minutes" to address the inline comment; reply to the inline comment noting the change.
4. Present plan. User says "yes but leave the last reviewed date alone, I update it manually."
5. Adjust plan: don't touch "Last reviewed" date.
6. Build new body: full original body, with only the rollback section and the "15 minutes" phrase changed.
7. Update with `version.number + 1`.
8. Re-read. Confirm panels and tables intact.
9. Reply to the inline comment: "Updated to 30 minutes."

That's the whole loop. Boring, but boring is the goal.
