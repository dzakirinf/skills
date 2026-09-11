PULL REQUEST STANDARD

TITLE
The title is the squash-merge commit subject. Write it as one.

- `KEY-123: what the change does` - ticket key, colon, space, then the
  summary in lowercase imperative mood. No trailing period.
- Two tickets in one PR: `SYS-3505 + SYS-3510: bound every cross-tenant
  fetch, and keep the operator key out of the log`. More than two means the
  PR is doing too much.
- Under ~70 characters. GitHub truncates past that in list views, and the
  tail it cuts is the half that says what you did.
- Name the change, not the area: "shape request bursts with a leaky bucket",
  not "rate limiter changes" or "fixes".

SHAPE
The body is diagrams, code, and tables, connected by bullets. Prose is the
connective tissue, not the substance. Three paragraphs in a row is an essay -
cut it.

- Every body carries at least one mermaid diagram or code snippet, unless the
  change has no shape worth drawing: a version bump, a config value, a one-line
  fix.
- The text between them is bullets, not paragraphs.
- Snippets may show internals or sample usage. Prefer the code a reader would
  otherwise have to open the diff to find.
- Code references (path:line) are welcome.

The body describes the squash-merge commit as it will land. Nothing about how
the branch got there.

NEVER
- A branch slug as the title: `fix/leaky-bucket-burst-shaping`, or a ticket
  key mangled by one: `Sys 2271 capture receipt date`. The same words as a
  sentence are fine; the slug form is not.
- A title inherited from the branch name without checking it against the
  final diff. The branch was named before the work; the title describes what
  landed.
- A bare ticket key with no summary after it.
- Test narration. No "Test plan", "Validation", "How I tested", "I ran the
  suite", "all tests pass", "verified locally". That the change works is
  assumed, and CI reports it.
- Intermediate PR history: an earlier diff size, a commit later rewritten, an
  approach tried and abandoned, review rounds. The reader sees only the final
  state, so only the final state is worth commentary.
- The diff restated in prose. If a bullet says what the diagram or the snippet
  already shows, drop the bullet.
- Filler headings with nothing under them but a restatement of the title.

BENCHMARKS
Numbers go in a table, never in prose. Label both sides:
  - baseline: measured on the target branch
  - candidate: measured on this branch
Measure the two back to back: same machine, same command, same iteration
count. State the unit and what was measured. A number without a baseline is
not a benchmark.

| Metric            | Baseline (main) | Candidate | Change |
|-------------------|-----------------|-----------|--------|
| p99 admit latency | 1.8 ms          | 0.4 ms    | -78%   |
| Allocations / op  | 14              | 2         | -86%   |

THE LONG FORM
A change that is genuinely difficult, high-risk, or wide in scope earns a
technical blog post: why the old shape failed, what had to change and in what
order, before/after code, diagrams of the moving parts. The size of the change
grants this exception - it is not a style you may choose. A long body on a
small change is the most common way to fail this standard.

---
EXAMPLES (illustrations only - describe your own change, do not copy these)

BAD - slug title, essay, narrates tests, mentions how the branch got here:

    fix/ratelimit-leaky-bucket-rework

    ## Summary

    This PR reworks the rate limiter. Originally this change was much larger
    (+6k lines) but after review feedback I split out the metrics work into a
    follow-up, bringing it down to around +1k. The core idea is that the token
    bucket was allowing bursts of up to twice the configured rate to be
    admitted in full, which caused downstream saturation during traffic
    spikes. I switched it to a leaky bucket instead.

    ## Test plan
    - Ran the full suite locally, all 412 tests pass
    - Verified manually against staging

GOOD - same change:

    SYS-1841: shape request bursts with a leaky bucket

    Token bucket admitted full bursts of up to 2x the configured rate. Leaky
    bucket shapes them to the rate instead.

    ```mermaid
    flowchart LR
      A[requests] --> B{bucket}
      B -->|<= rate| C[admit]
      B -->|> rate| D[queue]
      D -->|drain at rate| C
    ```

    - Admission is now O(1) with no timer: `internal/ratelimit/leaky.go:41`
    - `Allow` keeps its signature, so no caller changes

    | Metric            | Baseline (main) | Candidate | Change |
    |-------------------|-----------------|-----------|--------|
    | p99 admit latency | 1.8 ms          | 0.4 ms    | -78%   |


BAD - the long form spent on a trivial change:

    ## The problem with implicit timeouts
    (900 words, three diagrams, a history of the codebase)

    ...for a change that bumps one constant from 30 to 60.

GOOD:

    DEVOPS-410: raise the export client timeout to 60s

    - HTTP client timeout 30s -> 60s. The upstream export endpoint takes up to
      45s on the largest tenant.
