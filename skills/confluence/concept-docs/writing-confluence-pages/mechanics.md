# Confluence mechanics (Atlassian MCP)

How to read, write, and verify Confluence pages. The Atlassian tools are **deferred** — load them first with `ToolSearch` (e.g. `select:mcp__plugin_atlassian_atlassian__getConfluencePage`), then call them.

## cloudId
Pass the **site hostname** directly as `cloudId` — e.g. `your-site.atlassian.net`. No lookup step needed.

## Tools by job

| Job | Tool |
| --- | --- |
| Read a page body | `getConfluencePage` (contentFormat `markdown` to read, `html` to round-trip for editing) |
| **Reconcile** a cluster — list the real child tree | `getConfluencePageDescendants` (the index-vs-reality check) |
| Read review comments | `getConfluencePageFooterComments`, `getConfluencePageInlineComments` |
| Find a page / its ID | `searchConfluenceUsingCql` (e.g. `space = PLAT AND title = "..."`) |
| **Create** a new page | `createConfluencePage` |
| **Update** an existing page | `updateConfluencePage` |

## Authoring bodies (contentFormat `html`)
- Lead (every page): the one-or-two-sentence lead, as an info panel **wrapped in an Excerpt macro** so the parent's child-index can surface it. Author it as a bodied-extension (reverse-engineered from the editor):

  ```
  <div data-type="bodied-extension" data-extension-key="excerpt" data-extension-type="com.atlassian.confluence.macro.core"><div data-type="panel-info"><p>…a sentence or two…</p></div></div>
  ```

  The Excerpt's `name` param is optional and is **not** shown in the index (naming is harmless); it displays on the page by default. The child-index surfaces the excerpt's **content in full** (but small) — so keep the lead to a sentence or two. **Verify** it renders in-browser.
- Real page link / smartlink: `<a href="https://<site>/wiki/spaces/<KEY>/pages/<ID>/<Slug>" data-card-appearance="inline">Title</a>` (renders as a live smartlink)
- Status lozenge (use sparingly — usually a sign of a status log): `<span data-type="status" data-color="green">Label</span>`
- Headings `<h2>`, tables, and lists are standard HTML.
- Get child / related page **IDs first** (via `getConfluencePageDescendants` or `searchConfluenceUsingCql`) so links are real, not bold placeholders.
- Child index (a hub's *Pages in this space*): a self-rooted macro, not a hand-written list — one authoritative index that reads the live children. Use the **Child pages** macro (`children`, schemaVersion 2): it is rooted at the page it sits on, so no root param is needed. Author it as a native **block** node with the **nested** param shape — a flat `{"depth":"1"}` is silently dropped; params must be `macroParams` objects. Use the macro's own ADF param names (reverse-engineered from what the editor writes — the legacy storage names like `all`/`sort` are **not** what this macro reads):

  ```
  <div data-type="extension" data-extension-key="children" data-extension-type="com.atlassian.confluence.macro.core" data-parameters="{&quot;macroParams&quot;:{&quot;depth&quot;:{&quot;value&quot;:&quot;1&quot;},&quot;allChildren&quot;:{&quot;value&quot;:&quot;false&quot;},&quot;excerptType&quot;:{&quot;value&quot;:&quot;simple&quot;}}}"></div>
  ```

  - `depth=1` + `allChildren=false` → direct children only. The param is `allChildren`, **not** `all` (`allChildren=true` shows the whole descendant tree and ignores `depth`).
  - `excerptType=simple` is exactly what the editor's **Excerpt preview** toggle writes, and it's the value that turns excerpts on. `rich` is **not** honored by this macro version — it leaves the toggle off and renders no excerpt.
  - **Scent has a cost:** `simple` shows an excerpt **only from each child's own Excerpt macro** — a child without one lists as a bare title. So this index moves scent off the hub and onto each child (its arguably-right home), at the price of one Excerpt macro per child. Weigh that before choosing the macro over a short hand-written list.
  - **Not `pagetree` (Page Tree):** the MCP round-trips it into a broken *inline-extension*, and it has no max-depth param. The Content Tree / Page Tree macro is fine when a **human** inserts it in the editor — only the MCP path is broken.
  - **Verify:** fetch back and confirm the block stored with `data-extension-key="children"` and your `macroParams`; then open the page in a browser — excerpts render only if the children carry Excerpt macros (the API returns the macro node, not rendered output).

  If the MCP can't author it, leave a placeholder instead: `<div data-type="panel-info"><p>TODO — human: add a Child pages macro (depth 1, Excerpt preview on); give each child an Excerpt macro for scent.</p></div>`.

## Approval & verification
- **Propose before applying.** Present the draft (new page) or the per-page change-set (revision) for approval *before* calling `createConfluencePage` / `updateConfluencePage`. Don't write live pages unprompted.
- **Verify after writing** — fetch the page back and confirm links rendered as `smartlink` and the structure is intact.
- **Dangling comments are expected.** Editing the text an inline comment is anchored to leaves that comment dangling once the request is fulfilled; the page owner resolves it (the API can create comments but not resolve them).
