# concept-docs

Three skills for authoring and maintaining **Confluence documentation clusters** — the multi-page, narrative docs you write *after* brainstorming a new concept, platform, or cross-cutting initiative (e.g. "Event Pipeline", "Software Supply Chain Security").

It is **not** a replacement for templated, single-page operational docs (runbooks, ADRs, postmortems, single-component infrastructure pages) — those stay with whatever templated-doc tooling you already have. Different ballgame.

## The three skills

| Skill | Fires when | Owns |
| --- | --- | --- |
| `creating-confluence-clusters` | You're publishing a **new** concept as a cluster | IA placement, decomposition, link wiring, creation |
| `revising-confluence-clusters` | An **existing** cluster needs cleanup (comments or audit) | Index↔descendants reconciliation, audit, scoped edits |
| `writing-confluence-pages` | Per **single page** — invoked by the two above, or directly | Role→structure, content principles, mechanics |

**How they compose:** the create and revise skills own *cluster-level* work and call `writing-confluence-pages` once per page for the page-level structure. Roles are recursive — a page can be a hub (has children) and content (carries explanation) at once — so the page skill composes a hub pattern + a content pattern from the page's properties rather than picking one identity.

## Using the skills

The skills are **model-invoked** — Claude picks the right one from your request — or you can name one explicitly (e.g. `/revising-confluence-clusters`). They accept a Confluence page **URL**, **page ID**, or tiny link; cloudId is just the site hostname (e.g. `your-site.atlassian.net`).

**Revise an existing cluster** (most common) — point it at the cluster's **overview page**. It **maps the whole subtree cheaply** (titles/links, no bodies) to catch tree-wide structural defects, then **deep-audits a bounded scope — the page you gave it + its direct children by default** — and shows you per-page change-sets **before writing anything**. Grandchildren and deeper are an offered follow-up pass, not an automatic 10-page rewrite.

> "Audit and revise the devstack cluster: `https://your-site.atlassian.net/wiki/spaces/PLAT/pages/123456789`"

Flow: map subtree (cheap) → reconcile index/links → **propose audit scope for approval** → deep-audit in-scope pages → **present diffs for approval** → write (`updateConfluencePage`) → verify → offer the next level. Nothing is written without your go-ahead. It roots the cluster at whatever page you give it.

**Create a new cluster** — give it a concept you've brainstormed and roughly where it belongs. It proposes IA placement + the page breakdown, drafts each page, and asks before creating.

> "Document the new 'Egress Proxy' platform as a Confluence cluster under Infrastructure."

**Fix a single page** — use `writing-confluence-pages` directly when you only want one page reshaped to its role.

## Core principles

- **Durable what/why, not a status log.** No ticket IDs, no rollout state. Keep *durable reference* (e.g. ISO-control tables); cut *time-bound status* (adoption lozenges) and relocate it to the tracker.
- **Structure follows the page's role in the IA**, not taste.
- **Real page links** (smartlinks), noun headings, precise terminology.
- **The audit is the job** — a page with no comments is not necessarily clean.

## Installing

```bash
npx skills add dzakirinff/skills --skill creating-confluence-clusters --skill revising-confluence-clusters --skill writing-confluence-pages
```

Take all three. `creating-` and `revising-` both invoke `writing-confluence-pages`
per page and are incomplete without it. Then start a new session; skills are read
at startup.
