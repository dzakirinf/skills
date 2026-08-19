# ticket-drift

A backlog ticket written three months ago proposes a mechanism you replaced last
week. It still reads fine, so whoever picks it up builds the wrong thing, and their
fix may undo yours.

This skill sweeps the backlog after you finish work, finds the tickets your work
invalidated, and leaves two things on each: a comment explaining what moved, and a
dated one-line note on the description so the comment gets read *before* the work
starts rather than after.

It was built from a real case, checked in as `references/worked-example.md` with
the org's identifiers changed. PLAT-431 shipped, and PLAT-206 — sitting in a
different epic under someone else's name — had been quietly proposing the mechanism
431 replaced. The comment that went on it is the voice the skill writes in.

## Installing

```bash
npx skills add dzakirinf/skills --skill ticket-drift
```

Then start a new session. `/ticket-drift` becomes available as a command, and the
skill also fires on its own — see below.

## Running it

There are two dials. Scope is the finished work whose fallout you're chasing, and
candidates are the tickets checked against it.

| What you want | What to type |
|---|---|
| Fallout of one finished ticket | `/ticket-drift PLAT-431` |
| Fallout of a whole finished epic | `/ticket-drift PLAT-410` |
| Is my own epic self-consistent? | `/ticket-drift internal audit of PLAT-410` |

The first two check the whole project board. The third checks only that epic's own
children, 13 rows instead of 191, and it has the best hit rate of the three: an
epic's own backlog was written against the pre-merge world by the people who then
went and changed those same files. It's cheap enough to run every time a child
ships.

Prefer epic scope over running it once per finished child. Drift is caused at epic
granularity, so a per-child run can only produce the fraction of the verdict that
child explains, and it re-sweeps the same board once per child.

It also fires on its own from things like "just merged PLAT-431" or "what should
I work on next". That's deliberate, because forgetting to run it is the failure it
exists to fix. An unbidden run still stops at the gates below, so the most it can
cost you is tokens.

## What happens

Nothing is written until you say so. There are two gates:

1. The findings table gives you one row per drifted ticket: key, summary, verdict,
   the evidence, and whether you own it. Above it sit the count swept and the count
   dropped. Nothing is drafted yet, and you name which tickets proceed.
2. The drafts come next, only for the tickets you picked. Every write is separately
   approvable: the comment, the description note, and on your own tickets the
   re-scope and the close. Approving a comment is not approving a close.

Then it posts, and reports what it wrote and what it left alone.

**The gates are instructions, not a lock.** Posting goes through the Atlassian MCP
write tools, which your permission mode governs. The skill will always stop and
ask, but if you want it *unable* to post without you, that's a permission setting,
not something the skill can promise about itself.

## What it writes

A ticket you don't own gets exactly two writes: one comment, and one dated note at
the end of the description. Status, assignee, priority and the existing description
body are left as they are. The skill establishes the facts and names the decision,
and the owner makes it.

A ticket you own gets those two, and optionally a description re-scoped to what the
ticket should now say, plus a close. Both of those are approved separately. Expect
this path to carry most of an internal epic audit.

## Requirements

- acli, authenticated. Check with `acli jira auth status`. This one is required: it
  runs the sweep, and it can run everything else too.
- Atlassian MCP, connected. Check with `/mcp`. Preferred for reading each candidate
  and for the writes, but optional, because the skill falls back to acli when MCP
  isn't authenticated. That happens often enough to matter.

Sweep runtimes, measured 2026-08-13: 2.6s for a 13-row internal audit, 8.9s for a
191-ticket board, 23.5s for a 466-ticket one.

## When not to reach for it

- Adjacency isn't drift. If a ticket still says what it should do, the skill skips
  it rather than posting an FYI. Board attention is the scarce resource, and a run
  of FYI comments is how people learn to skip these.
- It doesn't rank your backlog. Point it at a candidate and it'll tell you whether
  that ticket has drifted, but deciding what to work on next is a different job.
- It doesn't close, transition, or reassign other people's tickets. That's by
  design, and there's no flag to change it.

## Editing it

`SKILL.md` holds the behaviour and is the only source of truth for it. This README
deliberately doesn't restate the verdict list, the comment anatomy, or the sweep
mechanics, so the two can't fall out of sync.

Two rules in there look like fussy detail and aren't. One is the `--paginate` and
row-count-versus-`--count` check in step 2. The other is the note under Tooling on
why the sweep doesn't use MCP. Both were found by measuring rather than reasoning,
and each guards a failure that returns exit 0 with a plausible empty result. Read
them before simplifying either.
