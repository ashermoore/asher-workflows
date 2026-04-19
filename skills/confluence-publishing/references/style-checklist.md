# House Style Checklist (detailed)

This is the long-form version of the checklist in `SKILL.md`. Each rule has a short rationale and a worked example so you can see exactly what to produce and what to avoid.

## 1. No title repeat

Confluence renders the page title at the top of every page. Repeating it as the first line of the body wastes the most valuable vertical space on the page.

**Avoid:**

```markdown
# Q2 Launch Runbook

This is the runbook for the Q2 launch...
```

**Prefer:** Start directly with the first real section.

```markdown
## Purpose

This runbook covers the cutover for the Q2 product launch...
```

If you genuinely need a subtitle (like a status tag or a one-line summary), use a panel or a short italic line, not an H1.

## 2. No horizontal rules

`---`, `***`, and `<hr>` render as thin grey lines in Confluence. They add visual noise and fragment the page without providing structure that the reader can navigate.

**Avoid:**

```markdown
Some content.

---

More content.
```

**Prefer:** Use headings (`##`, `###`) and a blank line.

```markdown
Some content.

## Next section

More content.
```

## 3. No em dashes

The em dash character (Unicode U+2014, written as `\u2014`) looks elegant in prose but is often a symptom of a sentence trying to do two things. Replace with a comma, colon, full stop, or parentheses, or rewrite.

En dashes (the slightly shorter U+2013) in number ranges like 10 to 20, and ordinary hyphens in compound words, are fine. The rule is about the long dash specifically.

**Avoid (with em dash):** "The service was slow \u2014 calls took 8 seconds \u2014 until we added caching."

**Prefer (with commas):** "The service was slow, with calls taking 8 seconds, until we added caching."

Or split the sentence: "The service was slow: calls took 8 seconds. We fixed this by adding caching."

A quick search to catch them before publishing: look for the unicode codepoint `\u2014` in the body.

## 4. British English

Use British spellings throughout. Common swaps:

| American | British |
|---|---|
| organize, organization | organise, organisation |
| realize, recognize | realise, recognise |
| prioritize, optimize, analyze | prioritise, optimise, analyse |
| color | colour |
| behavior | behaviour |
| flavor, favor | flavour, favour |
| center, theater, meter, liter | centre, theatre, metre, litre |
| gray | grey |
| traveled, canceled, modeled | travelled, cancelled, modelled |
| defense, offense, license (noun) | defence, offence, licence (noun) |

Verb/noun pairs to watch:

- **licence** (noun), **license** (verb). "A driving licence"; "we license the software".
- **practice** (noun), **practise** (verb). "Good practice"; "we practise weekly".

Before publishing, do a find for the common American forms: `ize`, `ization`, `color`, `behavior`, `center`, `gray`, `analyze`. Accept technical terms like `utilize` only where they are part of an API name or code identifier.

## 5. Working @ mentions

Plain text `@asher` does not link to a user, does not notify them, and looks amateur. Use real Confluence mention markup.

If the user names a person by display name, ask for their Atlassian account id or username before publishing. Do not guess.

If you have a list of owners in a table, every row should be a live mention:

```markdown
| Area | Owner | Backup |
|---|---|---|
| Payments | @alice.chen | @bob.ng |
| Search   | @carol.sm  | @dev.patel |
```

If you genuinely do not have the handle, use the full name without the `@` and flag it in your plan so the user can fill it in.

## 6. Visual callouts

Confluence supports several panel types. Use them to draw attention to things that would otherwise be missed in prose.

| Panel | When to use |
|---|---|
| **Info** | Background context, links to related pages, "good to know" |
| **Note** | Asides and clarifications that are useful but not urgent |
| **Success** | Confirmations, completed milestones, known-good state |
| **Warning** | Risks, caveats, non-obvious gotchas |
| **Error** | Known broken states, things that will fail if done wrong |

Reach for a panel whenever you catch yourself writing:

- "Note that…"
- "Be aware…"
- "Important:…"
- "Action required:…"
- "⚠️ …"

Rule of thumb: at most one panel per major section, and no more than three panels on a page in total. Two panels back-to-back already start to lose attention; three in a row dilutes the signal entirely. If you find yourself wanting more, fold them into prose or into a single panel that lists the items.

See `storage-format.md` for the exact Markdown-to-panel syntax the Atlassian MCP accepts.

## 7. No repetition

After drafting, read the page top to bottom in one pass. For each paragraph, ask: "Did I already say this?" If yes, cut one copy. Particular hotspots:

- Introductions that restate the page title
- Conclusions that restate the introduction
- Section intros that preview the bullets below
- "As mentioned above, ..." constructions

A good page says each thing exactly once, in the most natural place, with enough context that the reader does not need the reminder.

## 8. Logical flow

A reader who knows nothing about the topic should be able to follow the page top-to-bottom without jumping back and forth. A rough order that works for most internal docs:

1. **Purpose / TL;DR**, one or two lines on what this page is for and who it's for.
2. **Context**, the background a new reader needs to make sense of what follows.
3. **Substance**, the actual content: the runbook steps, the design, the analysis.
4. **Next steps / calls to action**, what the reader should do after reading, and who owns what.
5. **Appendix / reference**, raw data, deep links, long tables that support the substance but don't need to be read linearly.

If you find yourself writing "as mentioned above" or "see below", the structure is probably wrong: the reference target should be consolidated and live in one place.

## 9. Respect existing comments

An inline comment is anchored to a specific span of text in the page body. If you rewrite that span, the comment loses its anchor and becomes confusing ("about what?").

Before editing:

- Call `getConfluencePageInlineComments`. For each open comment, note the anchor text and the ask.
- If your planned edit would change the anchor text, decide consciously: address the comment (edit in the way it asked and reply), or preserve the anchor and address the comment differently, or add a footer comment explaining what happened.
- Never silently rewrite a commented paragraph. Commenters notice, and it reads as dismissive.

## 10. Respect manual edits

Users type directly into Confluence. Your chat history does not show those edits. The live page is the source of truth.

Before any update:

1. Read the current page body.
2. Compare it to what you last published or what you think should be there.
3. Anything you did not produce is a candidate manual edit. Do not silently overwrite it.
4. If the user's request implies overwriting a manual edit, flag it: "You've added a paragraph about X in section Y. My plan would rewrite that, do you want to keep it, move it, or replace it?"

This is the single biggest source of user frustration with agent edits, and the cheapest to fix: ask.
