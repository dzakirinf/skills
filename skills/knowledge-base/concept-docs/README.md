# concept-docs

Three skills for authoring and maintaining Confluence documentation clusters:
the multi-page narrative docs you write after brainstorming a new concept,
platform, or cross-cutting initiative, like "Event Pipeline" or "Software
Supply Chain Security".

They don't replace templated single-page operational docs. Runbooks, ADRs,
postmortems and single-component infrastructure pages stay with whatever
templated-doc tooling you already have.

## The three skills

| Skill | Fires when | Owns |
| --- | --- | --- |
| `creating-confluence-clusters` | You're publishing a new concept as a cluster | IA placement, decomposition, link wiring, creation |
| `revising-confluence-clusters` | An existing cluster needs cleanup, from comments or an audit | Reconciling the index against the descendants, the audit, scoped edits |
| `writing-confluence-pages` | A single page, invoked by the two above or directly | Role to structure, content principles, mechanics |

The create and revise skills own the cluster-level work and call
`writing-confluence-pages` once per page for the page-level structure. Roles are
recursive: a page can be a hub, meaning it has children, and content, meaning it
carries explanation, at the same time. The page skill composes a hub pattern and
a content pattern from the page's properties rather than picking one identity.

## Using the skills

The skills are model-invoked, so Claude picks the right one from your request,
and you can name one explicitly if you'd rather. They take a Confluence page
URL, a page ID, or a tiny link. cloudId is just the site hostname, like
`your-site.atlassian.net`.

Revising an existing cluster is the common case. Point it at the cluster's
overview page. It maps the whole subtree cheaply, titles and links but no
bodies, which is what catches the tree-wide structural defects. Then it
deep-audits a bounded scope, by default the page you gave it plus its direct
children, and shows you per-page change-sets before writing anything.
Grandchildren and deeper are an offered follow-up pass rather than an automatic
10-page rewrite.

> "Audit and revise the devstack cluster: `https://your-site.atlassian.net/wiki/spaces/PLAT/pages/123456789`"

The flow: map the subtree cheaply, reconcile the index against the links,
propose an audit scope for your approval, deep-audit the pages in scope, present
the diffs for approval, write them with `updateConfluencePage`, verify, then
offer the next level down. Nothing is written without your go-ahead, and it
roots the cluster at whatever page you hand it.

Creating a new cluster takes a concept you've brainstormed and a rough idea of
where it belongs. It proposes the IA placement and the page breakdown, drafts
each page, and asks before creating anything.

> "Document the new 'Egress Proxy' platform as a Confluence cluster under Infrastructure."

Use `writing-confluence-pages` directly when you only want one page reshaped to
its role.

## Core principles

- Durable what and why, not a status log. No ticket IDs and no rollout state.
  Keep durable reference such as ISO-control tables, and cut time-bound status
  such as adoption lozenges, which belongs in the tracker instead.
- Structure follows the page's role in the IA rather than taste.
- Real page links (smartlinks), noun headings, precise terminology.
- The audit is the job. A page with no comments is not necessarily clean.

## Installing

```bash
npx skills add dzakirinff/skills --skill creating-confluence-clusters --skill revising-confluence-clusters --skill writing-confluence-pages
```

Take all three. `creating-` and `revising-` both invoke `writing-confluence-pages`
per page and are incomplete without it. Then start a new session; skills are read
at startup.
