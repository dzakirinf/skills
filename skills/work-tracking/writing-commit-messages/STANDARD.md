COMMIT MESSAGE STANDARD

SUBJECT
- Imperative mood: "Add", "Drop", "Rename" - never "Added", "Adding", "Fixes".
  An instruction to the codebase, not a sentence about it: "Read the password
  from the container env", never "The scripts read the container's password".
- Under 72 characters. No trailing period.
- Names what changed, specifically enough that someone who knows the codebase
  can guess which lines moved. "Update handler" names nothing.
- Names the change, not the state after it. A subject can be specific about
  which files moved and still say nothing about what was done to them.
- Names the edit, not a goal for the repository. "Keep the helpers out of
  ui-e2e" is a wish; "Replace ui-e2e's re-exported helpers with stubs" is the
  edit. A behaviour the changed code now enforces is an edit, not a goal.
- Stands alone. A reader with no neighbouring commit in front of them must
  get it: no "too", "as well", "the rest", "same for X", "part 2".
- Plain words - the ones a colleague would use in review. No personification
  ("Let the container use its own password"), no coined terms of art ("the
  shared invocation", "the shaped wrappers"), no decorative verb where a
  plain one exists ("fold onto" for "replace"). Real identifiers, paths and
  domain nouns are reference, not voice - use them freely.

BODY
Include a body only if there is a constraint a future editor could break by
editing those lines. If there is no such constraint, the subject is the whole
message. Most commits have no body.

A constraint is a fact that the diff does not show and that makes the change
wrong to undo:
- an ordering or timing requirement
- an external contract: API shape, wire format, schema, on-disk layout
- a compatibility window that has not closed yet

Never a body:
- why the change was made
- evidence it works: test output, benchmarks, run IDs, log excerpts
- narration of the work: "investigated X, found Y, then tried Z"
- ticket keys, PR links, URLs
- the diff restated in prose

The argument goes in the PR and the ticket. The commit says what changed, and
what has to stay true.

---
EXAMPLES (illustrations only - describe your own change, do not copy these)

BAD - past tense, narrates the work, cites evidence:
    Fixed the flaky payment webhook test

    Investigated the intermittent failure and found the test asserted on
    wall-clock time. Reran the suite 50 times locally, passed every time.
    Run ID 8842193.

GOOD - nothing here a future editor could break, so no body:
    Freeze clock in payment webhook test


BAD - body is why the change was made:
    Switch session store to Redis

    We were seeing memory pressure on the app servers because sessions were
    held in-process, and Redis gives us horizontal scaling.

GOOD - same change, body says what must stay true:
    Switch session store to Redis

    Sessions must outlive a single process: the load balancer does not pin
    clients to an instance.


BAD - passes every mechanical check but names nothing:
    Update handler

GOOD:
    Reject webhook payloads over 1 MiB


BAD - specific about where, silent about what was done, and leans on the
previous commit for its meaning:
    The bash, python and RUNBOOK sites read the container's password too

GOOD - same change, names the action and stands alone:
    Read the demo scripts' mysql password from the container env


GOOD - an ordering requirement is a constraint:
    Run the schema migration before the backfill

    The backfill reads the new column; running it first leaves those rows
    unset.
