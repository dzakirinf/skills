---
name: creating-confluence-clusters
description: Turn a brainstormed concept into a new Confluence documentation cluster, placed in the space's information architecture.
disable-model-invocation: true
---

# Creating Confluence Clusters

## Overview

A genuinely new concept does not become *a page* — it becomes a **cluster**: one topic-overview page that explains the idea, plus child content pages that carry the depth. This skill is the workflow that comes *after* a brainstorm: place the concept in the information architecture, decompose it, and author each page.

It does **not** define what a good page looks like — that is the **REQUIRED SUB-SKILL: writing-confluence-pages**, which this skill invokes once per page. If it is not installed, stop and install it before drafting: `npx skills add dzakirinf/skills --skill writing-confluence-pages`. This skill owns the *cluster*: placement, decomposition, link wiring, approval, creation.

## When to use

You finished working out a new concept/platform/initiative (e.g. "Event Pipeline", "Software Supply Chain Security") and need to publish it.

**When NOT to use** — defer to the existing per-template tooling:

| Situation | Use instead |
| --- | --- |
| A single existing component you set up / onboard onto | a single-component infrastructure page |
| A procedure to run now / a decision / an incident | Runbook / ADR / Postmortem template |
| Reporting progress or status | nothing — status belongs in the tracker, not a doc |
| Revising an existing cluster | [[revising-confluence-clusters]] |

## Workflow

1. **Place it in the IA.** Find the right existing **root category** (don't invent one if a fitting category exists); the topic-overview page is created as its child. If the space has no fitting category, surface that rather than guessing.
2. **Decompose into 2–4 leaves** along the concept's **natural seams** (e.g. *threat model / implementation / rollout*) — never along tickets or sprints. A leaf may itself later grow children and become a sub-hub; that's fine and expected (roles are recursive).
3. **Assign each node its role and draft it** with **writing-confluence-pages** — the overview as a hub, each leaf as content (or both, if a leaf has its own children).
4. **Wire the links** so the cluster is navigable:
   - overview → each leaf (the child index),
   - each leaf → overview (the up-link — easy to forget),
   - leaves ↔ siblings where they relate,
   - and to any predecessor/superseded page (reciprocal links).
5. **Present the whole cluster draft for approval BEFORE creating anything.** Show the tree + each page's body. Do not create live pages unprompted.
6. **Create** parent-first with `createConfluencePage` (so children can reference the parent's ID), then **verify** by fetching back — links should render as smartlinks.

## Output

Approval-first, then write. Tool names and HTML node syntax live in writing-confluence-pages → `mechanics.md`. The create verb is `createConfluencePage` (vs `updateConfluencePage` for the revise skill).
