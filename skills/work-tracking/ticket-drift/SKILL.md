---
name: ticket-drift
description: Flag backlog Jira tickets that finished work has made obsolete, superseded, or already-resolved — a comment plus a dated description pointer.
disable-model-invocation: true
---

# Ticket drift

**Drift** is the gap between what a ticket says and what is now true. Work you
just finished creates it: a ticket written three months ago proposes a mechanism
you replaced, assumes a file that no longer behaves that way, or asks for
something already merged. The ticket still looks fine. Whoever picks it up finds
out the expensive way — and their fix may undo yours.

Closing that gap is two artifacts on each drifted ticket:

- **a comment** — what moved, the evidence, and how far the verdict actually reaches
- **a pointer** — one dated line on the description, so the comment gets read
  before the work starts rather than after

The threshold is drift itself: comment where the ticket no longer says what it
should do. A ticket merely adjacent to your work stays untouched. Board attention
is the scarce resource, and a run of FYI comments is how people learn to skip
these.

## Step 1 — Establish what shipped

First fix the **scope** — the body of finished work whose drift you are chasing.
**Default to the epic, not the single ticket.** Drift is caused at epic
granularity: PLAT-206 drifted because devstack moved to published images, which is
PLAT-410's thesis rather than any one child's commit. A per-child scope can only
produce the fraction of the verdict that child explains, and the skill's own rule
to scope verdicts to the evidence then forces a vaguer comment onto someone else's
ticket. One epic-wide run beats one run per Done child, which also re-sweeps the
same backlog N times and risks stacking a second comment on the same candidate.

Then write the change list. Three sources, and every item names which one it came
from:

- **git** — the merged commits and PRs across the scope
- **the repo as it stands now** — what the code does after those merges. Open the
  file. A claim about current behaviour recalled from session memory is what puts
  a wrong comment on someone else's ticket.
- **Jira** — the scope's children, each by key *and current status*. Queued
  children count as evidence: a backlog ticket about to be superseded by a
  Selected-for-Development sibling needs to know that now, and the status is what
  lets the reader tell shipped from committed-but-unbuilt.

**Completion:** every claim you will later put in a comment sits on this list
with a commit, a file path, or a ticket key behind it. A claim that fails this
test does not go in a comment.

## Step 2 — Sweep for candidates

Pick the candidate width. Both run the same two commands — the count first, then
the sweep — and both are bound by the `--paginate` rule below:

```bash
acli jira workitem search --jql "<CANDIDATES>" --count
acli jira workitem search --jql "<CANDIDATES>" \
  --fields "key,status,summary" --paginate --csv > sweep.csv
```

- **`project = <KEY> AND statusCategory != Done`** — the whole board. The default
  after shipping. Same project as the scope: PLAT work sweeps PLAT, CORE work
  sweeps CORE. Cross-project supersession happens, but not often enough to be worth
  a second full backlog a run.
- **`parent = <EPIC> AND statusCategory != Done`** — the epic's own children only.
  An internal audit, and cheap enough to run every time a child ships: PLAT-410
  is 13 rows against the project's 191. Expect the own-ticket path to carry most of
  what it finds — an epic's internal clashes are usually yours to re-scope or
  close, not someone else's call to make.

**`--paginate` is the whole ballgame.** Without it acli returns 30 rows, exit 0,
no warning — on SYS that is 6% of the board reported as a finished sweep. The
count cross-check is what catches it: rows in `sweep.csv` minus its header must
equal `--count`. Measured 2026-08-13 — a 466-ticket board gave 466 both ways with
`--paginate`, and 30 without.

Shortlist off the CSV: could the change list have moved this ticket? Judge on the
summary alone and include on a maybe — step 3 drops tickets cheaply, and a ticket
skipped here is never seen again.

**Read the scope's own not-Done siblings first.** They are the densest drift zone
in the project: written against the pre-merge world, by the people who then went
and changed those same files. The instinct is to look outward at other epics, and
the sweep does cover them — but the hit rate is highest at home.

*Vetting a single named ticket instead of sweeping?* This step is that one
ticket. Everything else holds.

**Completion:** the CSV's row count equals `--count`, and every row is
shortlisted or skipped.

## Step 3 — Read the shortlist and rule

Per shortlisted ticket, read the full description **and every existing comment**.
Someone often already said what you were about to, sometimes better — that ticket
is handled, drop it.

Then rule: a verdict from the table below with its evidence, or dropped with a
one-line reason. Scope the verdict to what the evidence actually supports.
"Superseded" and "superseded for the base, local case untouched" send the next
person to different places.

**Completion:** every shortlisted ticket carries a verdict plus evidence, or a
reason it was dropped. Nothing is left in a maybe.

## Step 4 — Present the findings

Draft nothing yet. Show the verdict table: one row per drifted ticket — key,
summary, verdict, the evidence in a clause, and whether you own it. Above it, the
count swept and the count dropped.

This is a selection gate, not a formality. The user picks which tickets proceed. An
epic-wide sweep surfaces six or eight drifted tickets, and drafting all of them
before anyone has said which ones matter spends the effort twice over and buries
the choice in a wall of prose.

**Completion:** the user has named the tickets to proceed with.

## Step 5 — Draft, then get approval

For the selected tickets only, draft the comment and the pointer. Show them and
get approval per ticket — these land on a board other people read.

Each write is its own approvable line item, named as the thing it does: the
comment, the pointer, and on a ticket you own, the description rewrite and the
close. Approving a comment is not approving a close.

**Completion:** each drafted write is approved or dropped.

## Step 6 — Write, then report

Apply the approved writes. Report per ticket: what you wrote, and what you left
alone.

The report is also the check on this skill's own failure mode. A run that read
30 of 466 tickets and found nothing looks exactly like a run that read all 466
and found nothing. Give the count. If nothing drifted, say so plainly — that is
a real and frequent outcome, not a shortfall to dress up.

## Verdicts

Five, each with the evidence that earns it.

| Verdict | Means | Evidence |
|---|---|---|
| **superseded** | another ticket or merge now does the whole of this | the superseding ticket key and its current status, or the commit |
| **superseded in part** | some of it is done or moot, some is still live | which part went, and the residual named precisely |
| **premise no longer true** | the description asserts something about the code that has since changed | the file, and what it does now |
| **already resolved** | the work is merged and nobody closed the ticket | the commit or PR that did it |
| **newly blocked / unblocked** | a dependency appeared or cleared | the ticket or change that moved it |

**Superseded in part** is the common verdict and the one worth getting right.
Whole supersession is easy to spot and easy to say. Partial supersession is where
a ticket sits half-true for months, and naming the residual gap is the entire
value of the comment.

## Comment anatomy

Six beats. `references/worked-example.md` is a real comment written to this
shape — read it before drafting your first one. It is the voice target, and the
shape is far easier to see finished than listed.

1. **Purpose line.** Why the reader is being stopped. *"Heads up before anyone
   picks this up. Two things moved since this was written."*
2. **What shipped**, dated and named by key.
3. **The evidence**, enumerated. Specifics out of the repo, from step 1.
4. **The verdict**, scoped to exactly what the evidence covers.
5. **What still holds.** Not optional. The reporter saw a real problem; a comment
   that only negates reads as a dismissal of it, and the next person throws out
   the good part with the stale part. Say which part survives — and if your work
   hit that same problem, say so. It turns a correction into corroboration.
6. **The open question, and your writes.** Name the decision the owner now has,
   framed as theirs to make, and close with the fields you changed.

One colleague warning another: short declarative sentences, state the finding and
stop.

## The pointer

One line at the end of the description:

```
**Note (YYYY-MM-DD):** <one sentence on what moved>. Read the comment below before starting.
```

Its whole job is to catch someone who opens the ticket and never scrolls to the
comments. Keep it to that one sentence plus the instruction — the comment carries
the reasoning.

**There is no append.** Descriptions are ADF, and both write paths replace the
entire field — MCP's `editJiraIssue` and acli's `--description` alike. Every
"append" is a full overwrite of someone else's content, which makes this the one
step in the skill that can silently damage a ticket you do not own.

Round-tripping through markdown is what does the damage: a panel, table, code block
or inline mention comes back as flattened text, the write returns success, and the
description still reads plausibly. So:

**read ADF → splice one paragraph node onto the end → write ADF back → verify the
leading nodes are unchanged.**

That last step is the guardrail. Without it a mangled description is
indistinguishable from a clean edit. Both paths carry ADF, so the fallback is not
lossy either: MCP via `contentFormat` / `responseContentFormat: "adf"`, acli via
`--fields description --json` out and `--description-file` back in.

On a re-run, update the existing note node rather than adding a second. Two stacked
notes read as two separate events.

## What you write, and what you leave

**A ticket you do not own** gets exactly two writes: one comment, one pointer
line. Status, assignee, priority and the existing description body stay as they
are. You establish the facts; the owner rules on what they mean for their ticket.

**A ticket you own** gets those two, and may also get:

- the description rewritten to the new scope, with the comment recording what
  changed and why. The changelog preserves the old text; only the comment
  preserves the reasoning.
- a close, through a transition read off `getTransitionsForJiraIssue` for that
  specific ticket. A superseded ticket is usually "Won't Do" rather than "Done",
  and the available names differ by project.

Both are separately approved in step 5.

**State your writes accurately in the comment's last line.** The worked example
signs off *"Leaving the description and status alone either way"* — and the
description did get a pointer. Accurate reads: status and assignee untouched,
note added to the description pointing here.

## Tooling

**acli** always runs the sweep — nothing else does it as cheaply. For the
per-ticket reads and the writes, prefer **Atlassian MCP** when it is connected and
fall back to acli when it is not. The tell is a server offering only `authenticate`
tools. Never stall at step 6 over it: every operation exists in both.

| Operation | MCP | acli |
|---|---|---|
| sweep | — see below | `workitem search --paginate --csv` |
| read candidate + its comments | `getJiraIssue`, `comment` in `fields` | `workitem view --fields "..." --json` · `workitem comment list` |
| post the comment | `addCommentToJiraIssue` | `workitem comment create -k KEY -F body.adf` |
| write the description | `editJiraIssue` | `workitem edit -k KEY --description-file d.adf` |
| close, own tickets only | `getTransitionsForJiraIssue` then `transitionJiraIssue` | `workitem transition -k KEY -s "<status>"` |

MCP `cloudId` takes the site URL in place of the UUID: `your-site.atlassian.net`.

**acli's `view` omits `parent` by default.** `--json` returns a five-field subset —
assignee, description, issuetype, status, summary — with exit 0 and nothing marking
the omission, so a missing `parent` reads as "this ticket has no epic". It has
already produced exactly that wrong conclusion about PLAT-431 once. Ask for what
you need: `--fields "parent,summary,status,description"` returns
`parent: PLAT-410`.

**Why the sweep is not MCP.** `searchJiraIssuesUsingJql` caps `maxResults` at 100
in its schema, and puts a `project` object with four avatar URLs on every row —
50 tickets came back 180KB, of which key and summary were 1.6%. The same
466-ticket sweep is 44KB through acli. Reaching for it anyway: pagination is
`issues.pageInfo.endCursor`, fed back as the `nextPageToken` argument. There is no
top-level `nextPageToken` in the response, despite the parameter's name.
