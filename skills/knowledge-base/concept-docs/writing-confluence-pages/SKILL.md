---
name: writing-confluence-pages
description: Reshape a single Confluence page to its role in a concept cluster — hub, content leaf, both, or root category.
disable-model-invocation: true
---

# Writing Confluence Pages

## Overview

One page, shaped to its **role**. A page's structure is not a matter of taste — it follows from what the page *is* in the tree. Two principles:

1. **Roles are recursive, not fixed by depth.** The same page can be a *hub* for its children and a *content* page for its own subject at once (e.g. a "Dependency Security" page that explains dependency security **and** indexes a child "Scanner Setup" page). Don't assume a page is only one thing.
2. **Documentation is durable what/why, never a status log.** No ticket numbers, no "Planned / In Review", no per-repo rollout state. Those rot; the concept doesn't. (The one nuance is below — durable reference vs time-bound status.)

This skill structures **one page** given its role. It does not decide IA placement or wire a whole cluster — the [[creating-confluence-clusters]] and [[revising-confluence-clusters]] skills own that and call this one per page.

## Step 1 — Read the page's role off two properties

Don't pick "overview vs leaf" as an identity. Read two independent yes/no properties; the structure composes from them.

| Property | Question | Adds |
| --- | --- | --- |
| **Hub** | Does it have child pages? | a what/why framing + an **index of its children** |
| **Content** | Does it carry the actual explanation of its subject? | **subject-driven sections** + the content principles |

- **Hub only** (top topic overview) → mostly index + what/why, little own content.
- **Content only** (true terminal leaf) → subject-driven sections, no index.
- **Both** (a mid-tree page that explains a sub-concept *and* has children) → what/why + own content + child index. This is common; expect it.
- **Root category** (a page that *defines a doc type* whose children are themselves topics) → a special terse shape (see below). You rarely author these.

## Step 2 — Apply the matching structure

**The lead — every page carries one, as an Excerpt.** Every page opens with a short lead — **a sentence or two** — saying what the page is, wrapped in an **Excerpt macro** and shown as an **info panel**. It does double duty: it orients a reader landing cold, *and* it is the scent the parent's child-index macro surfaces (the index shows the excerpt in full but small — so a sentence or two is the right size). It is the page's single summary — never write a second one elsewhere. Authoring the Excerpt macro: see [mechanics.md](mechanics.md).

### Hub pattern (a page with children)
1. **Lead** — the short Excerpt/info-panel lead (see *The lead* above), here as the hub's definition. Keep extra prose out of the panel; move it into *What it is*.
2. **What it is** — the thing itself, concretely, 1–2 short paragraphs.
3. **Why it exists** — motivation / threat / lineage; what it supersedes.
4. *(optional)* **The &lt;noun&gt; model** — one crisp paragraph stating the core guarantee or mechanism.
5. **Pages in this space** — a self-rooted **child-index macro** (a Content Tree / Child-pages macro), *not* a hand-written list: rooted at this page, depth 1, excerpts on. It reads the live children, so the index is a single source of truth — it can't drift and there's never a second list to sync. The exact macro and params (and the human-placeholder fallback): see [mechanics.md](mechanics.md).
6. **Related pages** — real links to lateral / predecessor pages.

### Content pattern (a page that carries explanation)
No fixed template — the subject drives the sections (a threat model is *thesis → attacker model → controls → residuals*; an implementation page is *components → lifecycle → validation*). Constant rules:
- **Opens with the lead** — the short Excerpt/info-panel lead (see *The lead* above), then the subject-driven sections.
- **Self-contained** — a reader landing here cold gets oriented.
- **Links up** to its parent (hub), and **across** to siblings.
- If it also has children, append the hub pattern's child index.

### Root category (rare)
One descriptive sentence (the Excerpt/info-panel lead, per *The lead* above), a **Use when**, and **Examples**. Nothing more.

## Content principles (every page)

| Principle | Do | Don't |
| --- | --- | --- |
| Durable, not a log | State the enduring what/why | Ticket IDs, "Planned/In Review", per-repo status |
| Reference ≠ status | Keep durable reference tables (e.g. an ISO-control mapping) | Keep time-bound status tables (adoption lozenges, rollout state) |
| Relocate, don't delete | When you cut status, ensure the info still lives in the tracker/epic | Silently drop information the owner relies on |
| Headings are nouns | "The security model", "Why it exists" | "The model in one line" (a compacted sentence) |
| Real cross-links | Inline page link / smartlink | **Bold text** naming a page |
| List vs prose | Bullet a **set** — parallel, labelled, independently-scannable items | Bury a labelled set in one running sentence ("Prevent — … Contain — … Detect — …") |
| Precise terms | Keep a primitive's real name (*microVM*) where it's the technical fact | Flatten everything and lose accuracy |
| One idea per page | Let children carry depth; link down to them | A mega-page duplicating a child's content |

**Durable reference vs time-bound status — the load-bearing distinction.** "No status" does *not* mean "no tables." An ISO 27001 Annex A control mapping is durable *reference* — keep it. A "Control Evaluation" table whose value is an `Adopted / In Progress / Deferred` lozenge column is a tracker view pasted into a doc — cut it, and move the adoption state to the epic. Test: *would this cell **rot** by next sprint?* If yes, it's status; relocate it.

**Term-preference nuance.** A request like "call it *sandbox*, not *VM*" governs casual usage; still keep the technical primitive's proper name (*microVM*) where that's the literal fact, and say you did so it reads as deliberate.

## Common mistakes

These aren't in the principles table above — they're structural traps the table can't catch:

- **Treating a page as one role.** Recursion is real — a content leaf with children needs the child index too.
- **Two child indexes** — one "Pages in this space"; collapse any second list (it usually exists only to smuggle in status).
- **Missing up-link** — every non-top page links back to its parent. Easy to forget; check it.

## Mechanics & grounding

- **How to fetch, write, and verify on Confluence** (cloudId, HTML nodes, the named MCP tools): see [mechanics.md](mechanics.md).
- **The IA frameworks behind these shapes** (Diátaxis, information scent): see [references.md](references.md).
