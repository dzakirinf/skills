---
name: writing-pr-bodies
description: Write a pull request title and body to a fixed standard - a ticket-keyed title, and a body of diagrams, code and tables connected by bullets. Use when opening a pull request, revising an existing PR title or description, or after pushing a branch that has no PR yet.
---

# Writing PR Bodies

## 1. Read the standard

Read `STANDARD.md` in this skill's directory. Read it - do not write from
memory of it.

## 2. Look at the change before describing it

```bash
BASE=$(gh repo view --json defaultBranchRef -q .defaultBranchRef.name)
git diff --stat "origin/$BASE...HEAD"
git log --oneline "origin/$BASE..HEAD"
```

The stat decides the form. A three-file change gets bullets and one diagram. A
change that restructures how components fit together earns the long form. This
step is done when you can name which form the body takes.

## 3. Make the diagram show the mechanism

A mermaid diagram earns its place by showing what the reader cannot get from
the file list: the new control flow, the state machine, the order of
operations, what now talks to what. A box-and-arrow drawing of the system as it
already was is filler.

Where the change is better shown in code than in boxes, use a snippet instead -
the internals that changed, or how a caller now uses it. Both is fine.

## 4. Title the change

`KEY-123: what the change does`, checked against the stat from step 2.

`gh pr create` falls back to the branch name or the first commit subject when
you don't pass `--title`. Both are drafts, neither is the title.

## 5. Write the body, then open the PR

Markdown does not survive `--body` on the command line. Write the body to a
file and pass the file:

```bash
gh pr create --title "KEY-123: ..." --body-file <path>
```

Every bullet, diagram and table in that file earns its place against the stat
from step 2.
