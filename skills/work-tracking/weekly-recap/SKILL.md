---
name: weekly-recap
description: Summarise last week's shipped work from GitHub to terminal and a dated file.
disable-model-invocation: true
---

# Weekly recap

Reconstruct what I shipped over the **last complete Monday→Sunday week** from GitHub. Print it,
and save it dated.

## Configure

One line to set, at the top of every run:

```bash
ORG=your-org        # the GitHub org or user that owns the repos
```

Identity resolves through `@me` on every query, which `gh auth` settles at run time, so the same
skill works unmodified from a second machine or a second account. If you keep work and personal
GitHub accounts, this is the difference between a correct recap and a silently empty one — `@me`
cannot pick the wrong login, a hardcoded handle can. Check `gh auth status` before believing a
quiet week on a machine with more than one account configured.

## Attribution

Every PR is opened by the person who did the work, so attribution is a filter rather than a
problem: `--author=@me` on the PR search and `--assignee @me` on the issue searches settle it in
one pass, with no tiebreak and no contested list.

**The issue is the unit of work.** A PR hangs off the issue it closes, via GitHub's own
`closingIssuesReferences`, so the Shipped table has one row per issue and the PRs pack into a
cell. A PR closing no issue still gets a row of its own.

## Steps

### 1. Fix the window

```bash
dow=$(date +%u)                                   # 1=Mon … 7=Sun
MON=$(date -d "-$((dow + 6)) days" +%F)           # Monday of the last complete week
SUN=$(date -d "$MON +6 days" +%F)                 # its Sunday
TZOFF=$(date +%:z)                                # +08:00
WIN="${MON}T00:00:00${TZOFF}..${SUN}T23:59:59${TZOFF}"
NAME=$(date -d "$MON +4 days" +%b%d-%Y | tr '[:upper:]' '[:lower:]')   # aug14-2026
PREV=$(date -d "$MON -3 days" +%b%d-%Y | tr '[:upper:]' '[:lower:]')   # aug07-2026
```

`$WIN` is the whole week as a GitHub search range, and every query carries **both** ends. An
open-ended `>=$MON` is harmless when the run happens inside the week it summarises; run on Monday
it swallows that morning's merges into last week's file.

Pass timestamps with an offset, not bare dates. A bare `$MON..$SUN` leans on GitHub's own reading
of where the end day stops, and Sunday-evening merges are exactly what this window exists to
catch. Both `--merged-at` and `--closed` accept the full form.

`$NAME` names the output file and `$PREV` the week before it, both keyed to the week's Friday. The
key follows the week being summarised, not the day I run, so a re-run or a catch-up run overwrites
one file rather than opening a second.

### 2. Gather

Three searches, all against historical data, all bounded at both ends:

```bash
# Merged — the PRs
gh search prs --owner "$ORG" --merged --merged-at "$WIN" --author=@me --limit 100 \
  --json repository,number,title,closedAt,url

# Closed — the issues that landed
gh search issues --owner "$ORG" --state closed --closed "$WIN" --assignee @me --limit 100 \
  --json repository,number,title,closedAt,url

# Opened — everything I filed, whoever it ended up with
gh search issues --owner "$ORG" --created "$WIN" --author=@me --limit 100 \
  --json repository,number,title,url
```

`--merged` is a boolean state flag. The date goes on `--merged-at`; passing a date to `--merged`
returns plausible-looking wrong results.

A result of exactly 100 is a cap, not a count — re-run that search per repo and merge, because a
silent cap reads as a quiet week.

An empty result is ambiguous in the other direction: valid syntax with no matches looks identical
to a qualifier GitHub silently ignored. When a search returns nothing and you expected something,
re-run it with the filters dropped one at a time until rows appear. The filter that brings them
back is the one that was wrong.

**Done when all three searches have returned under 100, or the ones that hit it have been re-run
per repo and merged.**

### 3. Hang each PR off its issue

One call per merged PR, which returns the link **and** the diffstat together:

```bash
gh pr view <n> --repo <repo> --json closingIssuesReferences,additions,deletions,changedFiles
```

`closingIssuesReferences` is populated by GitHub from the PR's closing keywords (`closes #42`,
`fixes org/repo#42`). It is the platform doing properly what a ticket key in a PR title does by
convention — trust it over parsing titles.

A PR whose `closingIssuesReferences` is empty closed no issue. Give it its own Shipped row; a
chore, a release, or a hotfix is still work.

**Done when every merged PR sits under exactly one issue row or its own row, and the rows account
for every PR in the search.**

### 4. Find what was handed to me

GitHub search has no qualifier for assignment *history* — only the current assignee is searchable.
So this section is an approximation in two steps, and the second step is what makes it honest.

Candidates first: assigned to me now, touched during the week, filed by somebody else.

```bash
ME=$(gh api user --jq .login)
gh search issues --owner "$ORG" --assignee @me --updated "$WIN" --limit 100 \
  --json repository,number,title,url,author \
  --jq "[.[] | select(.author.login != \"$ME\")]"
```

Then confirm each candidate against its timeline, which *is* historical, and keep only the ones
whose `assigned` event actually falls inside the window:

```bash
gh api "repos/<repo>/issues/<n>/timeline" --jq '[.[] | select(.event == "assigned") | .created_at]'
```

Skip the confirmation and this section fills with every old issue that happens to still be
assigned to you and got a comment on Tuesday — the table stops meaning "handed to me this week".

**Done when every candidate has either an `assigned` event inside the window or has been dropped.**

### 5. Write it up

Print to terminal, then write the same content to `~/weekly-recap/$NAME.md`, creating the
directory if absent. Read `~/weekly-recap/$PREV.md` first when it exists — carrying a thread
forward ("the retry work that opened last Friday closed Tuesday") is most of the value of keeping
the archive.

`~/weekly-recap/.runs/` holds this skill's own run logs. When a timer invokes it, its log is
already open there, so that directory is this skill's own rather than evidence of another process.

Shape — the mechanical tables carry the facts, and Rundown carries the reasoning:

```markdown
# Week of <Mon D> – <Sun D>

<N> PRs · <M> issues closed · <O> opened · <A> assigned · ~<ins>+/<del>- across <files> files

Repos: <repo> <n> · <repo> <n> · …

Merged by day: Mon <n> · Tue <n> · Wed <n> · Thu <n> · Fri <n> [· Sat <n>] [· Sun <n>]

## Shipped
| Issue | Summary | PRs |
One row per closed issue, ascending. Pack a repo's PRs into one cell
(`api#130 #131 #132 · web#551 #553`). A PR that closed no issue gets a
`*(no issue)*` row.

## Rundown
Group the PRs into the 2–4 arcs they actually form, named by what changed, with the
issue range. An arc spanning repos is one entry, not several. Record the judgement
calls — why a thing was built the way it was — and say plainly what a thread left
unclosed.

## Opened
| Issue | State | Summary |
What I filed this week, minus anything already in Shipped.

## Assigned
| Issue | State | Summary |
Only work handed to me, confirmed against the timeline. Omit the heading when empty.
```

Opened and Assigned go straight from heading to table — no lead-in sentence. Only Rundown carries
prose.

In Rundown an issue number is an **abbreviation**: the first time the section names one, expand it
with its title — `#42 (rate limiter drops requests inside a burst window)` — and use the bare
number everywhere after. This holds for numbers that appear only in passing, including inside a
quoted PR title; rewrite the quote rather than leave the reference undefined.

In Opened, drop the issues that already appear in Shipped. The remainder are the problems found
while working, which is the point of the section. `<O> opened` in the header still counts
everything filed, so it is deliberately larger than the Opened table's row count. Keep the header
number whole; the table is the one that subtracts.

Every weekday appears in the day line, zeros included (`Mon 0`) — an absent day reads as an
oversight rather than a quiet Monday. Saturday and Sunday are the exception: they appear only when
something merged, since a zero weekend is the normal case and printing it every week is noise. A
weekend that does show up is worth a line in Rundown.

**Done when the file is written and every issue number in Rundown has been expanded on its first
appearance — sweep for bare numbers before writing, since one that reaches the end unexpanded is a
defect.**
