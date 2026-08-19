---
name: audit-comments
description: Sweep a codebase for comments that earn no reader — edit narration, restatement, duplication, stale pointers.
disable-model-invocation: true
---

# Audit comments

A comment is a **changelog** when it only parses for someone who saw the previous version: what the code used to be, what changed, who asked, which ticket. Git stores that already. In the file it is load with no reader.

A comment is a **landmine** when it marks what breaks if you touch this — the hazard no one can infer by reading. Landmines are the sharpest keeper — the class that justifies the rest. Protect them.

Sort every comment into keeper or deletion, and report the deletions.

## 1. Scope

Source the repo owns — skip vendored, generated, and build output.

Templates and examples others copy rank first: a changelog comment there gets pasted into every downstream codebase, carrying ticket IDs its new readers cannot open.

## 2. Seed

1. Grep the files in scope for these patterns:

   ```
   previously|used to|the older|no longer|instead of|formerly|we now|now uses|changed (to|from)|was (changed|moved|renamed)|before this|legacy|historically|per (QA|review)|as discussed
   ```

2. Grep for ticket IDs, matching `[A-Z]+-[0-9]+`.

Grep seeds the sweep. It does not bound it. On the repo this skill was built from, those patterns caught 2 of 12 changelog comments — the other 10 narrated the edit without a single trigger word.

## 3. Read

1. Rank the files in scope by comment-to-code ratio. That ratio is where the sediment settles.
2. Read every comment, densest file first.

**Caution:** the ratio sets reading order, not coverage. Every comment in scope is classified before the report goes out, and a file grep opened and you never read is an unfinished audit.

## 4. Classify

Of each comment: **what does this tell you that the code doesn't?**

| Keep it when it explains | Delete it when it explains |
|---|---|
| Why the code is this way | Why it was changed |
| What breaks if you touch it | What it used to be |
| A hazard you can't infer by reading | That a review or ticket asked for it |
| The contract a function honors | That the author weighed another approach |

Three more shapes go too, though none of them narrates the edit:

- **A restatement of the name** (`// when your data was observed` on `observedAt`) — it survives only if it adds a unit, a bound, or a convention the name omits.
- **The same explanation twice** in one file — keep the copy sitting at the code it governs.
- **Line-numbered pointers into other files** (`foo.ts:100-141`) — stale on arrival. Keep the file, drop the range.

Resolve a pointer before you decide its fate. One that resolves gets qualified with the repo holding it; one that resolves nowhere is not a keeper. A path you never opened is a guess either way.

A comment may reach for the past when the past is a live constraint: the naive fix that broke last time. That is a landmine — rewritten as a rule about the code.

- **Keeper:** "The purge must run after the empty check, or a run producing nothing deletes live rows."
- **Changelog:** "We used to run these the other way round and it deleted rows."

## 5. Report

Group findings by file. Per finding: `file:line`, the comment, one line on what makes it a deletion, and its replacement — either nothing, or the shorter comment that survives.

Name the keepers you protected, so a later pass does not mistake a landmine for history.

Close on three counts: comment lines audited, lines deleted outright, lines trimmed in place.

Report the findings and stop — applying them is a separate pass.
