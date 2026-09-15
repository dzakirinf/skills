---
name: polish-plan
description: Audit the code inside an implementation plan for changelog comments and tautological tests.
disable-model-invocation: true
---

# Polish plan

An implementation plan carries the code an executor will type into the repo verbatim. Everything inside a fence **ships**: its comments become the repo's comments, its tests become the repo's tests. The prose around the fences does not — that is instruction to the executor.

Audit the shipping code now, while a finding costs a text edit rather than a commit.

## 1. Scope

The plan is the path given as an argument; with none, the newest file under `docs/superpowers/plans/`.

`audit-comments/` and `audit-tests/` ship beside this skill. Resolve both against this skill's own directory and confirm each `SKILL.md` is there before dispatching — a subagent handed no rules invents an audit and reports it with the same confidence as a real one. A missing file stops the run.

Read the plan and note how many tasks it has; step 3 checks coverage against that number.

The corpus is every fenced block in that file. Prose, task headers, `**Files:**` lists, and run commands sit outside it.

## 2. Dispatch

Both subagents go out in a single response, so they run concurrently.

Each reads its audit skill off disk rather than being handed a copy: the audit skills stay the single source of truth for their own rules, and they are user-invoked, so a subagent cannot reach them through the Skill tool.

**Comments** — `general-purpose`:

> Read `<audit-comments path>` and apply it to the fenced code blocks in `<plan path>`.
>
> Scope override: your corpus is the fences in that one file. Its §1 Scope and §3's ratio ranking do not apply — one file, no tree to walk. §2's grep patterns and ticket-ID match still do; run them over the plan to order your reading. Every comment inside every fence is classified.
>
> These comments ship — the executor types them into the repo. A changelog comment here narrates an edit that has not happened yet, and lands in the repo describing a previous version no reader ever saw. The plan's own prose is not a comment; it stays as written.
>
> Report per finding: task and step number, the plan's line number, the comment, what makes it a deletion, and its replacement — nothing, or the shorter line that survives. Name the landmines you protected. Close on: comment lines audited, deletions, trims.

**Tests** — `general-purpose`:

> Read `<audit-tests path>` and apply it to the test code in `<plan path>`.
>
> Scope override: your corpus is the fenced test blocks in that one file. Its §1 Scope does not apply — one file, no test tree to enumerate. §2's weak matchers still do; grep the plan for them to order your reading. Enumerate by task instead: every task's test block is classified.
>
> A plan answers the red question more directly than a repo can. The implementation the test will run against is printed a few steps below it, so read the pair together — when the Step 3 code satisfies the Step 1 assertion only because both were written in the same breath, the tautology is proven rather than suspected.
>
> Each Step 2 `Expected:` line is the plan stating its own red answer. An expected failure of "function not defined", or an import error, means the test pins the export existing — the first row of the skill's own table.
>
> `superpowers:writing-plans` ships this as its task template:
>
> ```
> assert result == expected      # Step 1
> return expected                # Step 3
> ```
>
> Every plan written from that skill inherits the shape, so check each task against it.
>
> Report per finding: task and step number, the plan's line number, the test name, the answer to the red question, and the verdict — delete, or the contract the assertion should pin instead. When the finding is against one assertion rather than the test, name the assertion. Close on: tests audited, findings raised.

## 3. Report

Merge both reports into one list ordered by task, so a reader walks the plan once rather than twice.

Per finding: `Task N / Step M`, plan line, the offending line, the verdict, the replacement.

Confirm coverage before printing: every task appears in a subagent's count, or is named as carrying no code. A task neither agent reached is an unfinished audit — dispatch it again rather than reporting around it.

Close on the merged counts, then ask whether to apply.

## 4. Apply

Only on an explicit yes.

Edit the plan in place, finding by finding:

- A comment deletion drops the line from the fence.
- A comment trim replaces it with the shorter line that survives.
- A tautology rewrite changes the assertion, and the Step 2 `Expected:` line with it — an expected failure still naming the old symptom lies to the executor.

Every finding is applied or declined with a reason. Close on the count applied.
