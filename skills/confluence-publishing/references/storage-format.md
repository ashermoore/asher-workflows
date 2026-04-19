# Confluence Storage Format: Markdown Gotchas

The Atlassian MCP accepts Markdown for page bodies and converts it to Confluence storage format (the XHTML-based internal representation). Most Markdown works. These are the places where it doesn't, plus the wiki-markup fallbacks.

## Panels (info, note, warning, success, error)

Confluence renders these as coloured callout boxes. In Markdown you get them via fenced blocks tagged with the panel type. The Atlassian MCP accepts the following syntaxes; if one does not render in your Confluence tenant, try the other.

**GitHub-flavoured alerts (often the most reliable):**

```markdown
> [!INFO]
> Background context the reader may want.

> [!NOTE]
> An aside or clarification.

> [!WARNING]
> A risk or a gotcha.

> [!TIP]
> A success/good-practice callout.

> [!CAUTION]
> A stronger warning for irreversible or breaking actions.
```

**Explicit panel fence:**

```markdown
::: info
Some text.
:::

::: warning
Some text.
:::
```

If neither of those renders, fall back to wiki markup in a raw block:

```
{info}Some text.{info}
{note}Some text.{note}
{warning}Some text.{warning}
{tip}Some text.{tip}
```

## @ mentions

A working mention needs the Atlassian account id, not just a display name. The MCP typically accepts:

```markdown
@[Display Name](accountId:557058:abc123-...)
```

or the shorter form if your tenant supports it:

```markdown
@accountId:557058:abc123-...
```

If you do not know the account id, you can search for it via the MCP (the `list_users_and_teams` style tools on some connectors, or by asking the user). A plain `@asher` does not create a mention.

In tables, every cell that holds an owner should be a real mention. Plain-text usernames in owner columns break notifications and look unprofessional.

## Tables

Basic pipe tables work:

```markdown
| Column A | Column B |
|---|---|
| foo | bar |
```

Gotchas:

- **Tables inside panels** sometimes do not render. Put the table first and add a short panel below if needed.
- **Multi-line cells** require `<br>` in the cell, not a real newline. Pipe tables are single-line per row.
- **Alignment syntax** (`:---`, `:---:`, `---:`) is accepted but often ignored visually. Do not rely on it.

For complex tables (merged cells, headers on the left, etc.), use raw HTML inside the Markdown:

```html
<table>
  <tr><th>Quarter</th><td>Q1</td><td>Q2</td></tr>
  <tr><th>Revenue</th><td>£100k</td><td>£120k</td></tr>
</table>
```

## Code blocks

Fenced code blocks with a language tag render with syntax highlighting:

````markdown
```python
def hello():
    print("hi")
```
````

Supported languages include `python`, `javascript`, `typescript`, `bash`, `sql`, `yaml`, `json`, `java`, `go`, `rust`, `kotlin`, `ruby`, `php`, `xml`, `html`, `css`, and `diff`. Unknown languages fall back to plain `text`.

## Status badges

Inline coloured status tags ("DONE", "IN PROGRESS", "BLOCKED") are a common Confluence idiom but are not standard Markdown. The MCP usually accepts:

```markdown
:status[DONE]{color=green}
:status[IN PROGRESS]{color=yellow}
:status[BLOCKED]{color=red}
```

If that does not render, fall back to bold uppercase in a table cell and apply colour via a panel or leave plain.

## Headings

Use `##` and `###` for sections. Do not use `#`: the page title is already H1, and a second H1 confuses the outline.

Keep the heading tree balanced: do not jump from `##` to `####`. The Confluence "Table of Contents" macro, if present, reads the heading structure to build its nav.

## Horizontal rules

Do not use them. See the style checklist. If you catch yourself wanting a visual separator, use a heading or a blank line.

## Links

Standard Markdown links work: `[text](https://...)`. For links to other Confluence pages, prefer the short link form `[text](/wiki/spaces/XY/pages/12345/Title)` when you have it, so the link keeps working if the page is renamed.

## Table of contents

If you want a ToC, use the macro form rather than authoring one by hand:

```
{toc}
```

A hand-authored ToC goes stale the moment someone reorders sections.

## Emojis and symbols

Emojis render as emojis. Confluence also has its own emoticon shortcuts like `:)` and `(tick)`; prefer the Unicode emoji so the content is portable.

Avoid decorative symbols in headings (`📌 Overview`, `🚀 Launch plan`). They look noisy in the outline and the sidebar.

## Attachments

Inline images: `![alt text](cid:attachmentFileName.png)` if the image is already attached. To attach a new image in the same request, most MCP tools require a separate upload step; see the MCP tool docs for the current flow.

## Raw HTML

Most inline HTML works, but `<style>`, `<script>`, and `<iframe>` are stripped by Confluence. If you need a complex layout, Confluence's native "Layouts" and "Panels" macros are safer than HTML.

## Round-trip caveat

Confluence may normalise the body you send: extra blank lines are collapsed, Markdown is re-flowed, and some constructs are rewritten into storage-format XML. This is why the post-update verification step matters: after an update, re-read the page and confirm the result looks the way you intended.
