---
name: revising-confluence-clusters
description: Use when auditing or revising an EXISTING Confluence concept cluster — driven by reviewer comments, OR by a principles audit when there are no comments (slop accumulates silently). Covers reconciling the overview's child index against the actual descendant tree, repairing up/down/sibling links, and stripping status/ticket logs while keeping durable reference. For creating a brand-new cluster, use creating-confluence-clusters instead. Requires the writing-confluence-pages skill.
---

# Revising Confluence Clusters

## Overview

An existing cluster drifts: leaves stop linking up, an overview's index falls out of sync with the real child tree, a content page silently turns into a status board. This skill audits and repairs a cluster as **one coherent pass**, not page by page.

It does **not** define what a good page looks like — that is the **REQUIRED SUB-SKILL: writing-confluence-pages**, invoked per affected page. If it is not installed, stop and install it before drafting: `npx skills add dzakirinff/skills --skill writing-confluence-pages`. This skill owns the *cluster-level* work: reconciliation, the audit, scope, approval, writing.

## The stance that matters

**Comments are one signal; the audit is the job.** A page with no comments is not a clean page — absence of comments just means no one wrote one. Always audit every page against the principles, *then* fold in any comments. (Pages that "looked fine" because they were uncommented routinely turn out to be AI-generated slop.)

## Workflow

Two gears: a **cheap whole-subtree map** (metadata only), then a **bounded content audit** (full bodies) over an approved scope. Never fetch every page body by default — a deep cluster is many large pages and an unreviewable pile of writes.

1. **Map the whole subtree — cheap, metadata only.** `getConfluencePageDescendants` on the page you were given (any depth) returns titles/IDs/depth/links in one call, **no page bodies**. From that metadata run the structural checks in [reconciliation.md](reconciliation.md): index ↔ actual children, missing up-links, orphaned grandchildren, supersession. These tree-wide defects cost almost nothing to find.
2. **Propose the content-audit scope and get approval BEFORE fetching bodies.** Default scope = **the page you were given + its direct children**. Report the total page count and the map (e.g. *"12 pages in this subtree; I'll deep-audit these 4 — say so to include a branch or go deeper"*). Deeper levels are a follow-up pass, never automatic. Structural-only fixes (an up-link, an index entry) on out-of-scope pages are offered with their level, not slipped in.
3. **For the in-scope pages, build the change list from BOTH sources:**
   - **Comments** — `getConfluencePageFooterComments` + `getConfluencePageInlineComments`; each becomes a concrete task.
   - **Audit** — apply the writing-confluence-pages principles to every in-scope page, including ones with no comments. This catches status logs, sentence headings, bold pseudo-links, and role drift.
4. **Set the edit scope by the strongest signal per page:** a structural ask or finding ("use a what/why structure", the index is a status board) → restructure to the role's shape; local asks only → targeted edits, don't rework a sound page; ambiguous aggressiveness → ask the author.
5. **Produce each in-scope page's change-set** with **writing-confluence-pages** (role, durable-reference-vs-status, "relocate, don't delete").
6. **Consolidate the change-sets, present for approval, then write** each with `updateConfluencePage`. Don't write live pages unprompted.
7. **Verify** (fetch back); fulfilled inline comments will show **dangling** — expected; the owner resolves them. Then **offer the next branch/level** as a follow-up pass.

## Cutting status without losing information

When the audit flags a status/ticket log, apply writing-confluence-pages' **relocate, don't delete** — the cluster-specific routing: adoption matrix → the epic, per-repo rollout state → the tracker. Say where it went, so the change reads as a relocation, not data loss. (What counts as status vs durable reference: the durable-reference-vs-status test in writing-confluence-pages.)
