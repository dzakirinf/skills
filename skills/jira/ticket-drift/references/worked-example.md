# Worked example — PLAT-206

The voice target. A real comment and pointer, with the org's identifiers changed.

**Situation.** PLAT-431 (publish devstack's images to GHCR) had just merged.
PLAT-206, "devstack: branch-config for compose (pin service refs regardless of
working-tree state)", sat in Backlog under a *different* epic — reported and
assigned to someone else, proposing a `.branches.env` + `scripts/up.sh`
mechanism that checked out pinned refs in sibling repos.

**Verdict.** Superseded in part. PLAT-432 rewrites the same file for the base
case; the local case the ticket also covers survives.

**Writes.** One comment, one pointer. Status, assignee and the description body
left as they were.

---

## The comment

Beat labels are annotations for you, not part of the posted text.

> **[1 — purpose line]**
>
> Heads up before anyone picks this up. Two things moved since this was written.
>
> **[2 — what shipped, dated and keyed]**
>
> PLAT-431 merged on 2026-08-13. devstack's seven own images, plus
> orders-api-devstack and orders-client, are now built in CI and published to GHCR.
>
> More relevant here: PLAT-432 ("devstack: reference published images in the
> deployable base", epic PLAT-410) is Selected for Development. All 15 services
> that still build from sibling checkouts do so in docker-compose.base.yml, which
> is the file 432 rewrites:
>
> **[3 — the evidence, enumerated out of the repo]**
>
> * ../../web-rails: web, web-2fa-enforced, pdf-service
> * ../../orders-api: orders-api, orders-api-rate-limit-enforced, orders-api-sso-enforced
> * ../../orders-client: orders-client
> * ../../console-ui: the four \*-bff and four \*-frontend
>
> **[4 — the verdict, scoped: "for the base", not wholesale]**
>
> So the mechanism proposed here, .branches.env plus a scripts/up.sh running
> `git fetch && git checkout --detach` in the sibling trees, is superseded for the
> base. 432 pins published image tags and never touches anyone's working tree.
>
> **[5 — what still holds; the reporter's problem corroborated, not dismissed]**
>
> The problem you described is real, and we hit it during 431. An
> adapter-toolkit checkout was 43 commits behind origin/main, so the stack
> ran reference adapters predating PLAT-398's enum contract while a fixture in
> the repo tested that same contract. Nothing failed. The two just disagreed
> silently.
>
> What 432 does not cover is the local case: a developer wanting the stack to run
> their own in-progress branch. Compose already exposes WEB_SRC, ORDERS_SRC,
> ORDERS_CLIENT_SRC and CONSOLE_UI_SRC for that.
>
> **[6 — the open question, left as the owner's call, plus the writes]**
>
> So the question is whether this re-scopes down to that local case, keeping those
> overrides deliberate, or closes in favour of 432 if a separate mechanism isn't
> worth it. Leaving the description and status alone either way.

The final clause is the one line to *improve* on, not copy: a pointer was added
to the description. See "What you write, and what you leave" in `SKILL.md`.

## The pointer

Appended to the end of the description:

```
**Note (2026-08-13):** PLAT-431 shipped and PLAT-432 covers part of what this proposes. Read the comment below before starting.
```

## What makes it work

- **Every specific is checkable.** Fifteen services grouped by sibling repo, one
  named file, four env vars, a commit count. Nothing asks the reader to trust a
  summary.
- **The verdict names its own limit.** "Superseded *for the base*" plus the local
  case called out — so a reader who cares only about the local case learns their
  half is still live, in one read.
- **Beat 5 does the diplomatic work.** The reporter's instinct is confirmed by a
  concrete incident from the finished work. The comment corrects the mechanism
  while backing the problem.
- **It stops at the question.** Re-scope or close is stated as the owner's
  decision, with both options spelled out — no transition, no reassignment, no
  rewrite.
