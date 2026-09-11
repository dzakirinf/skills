---
name: writing-commit-messages
description: Write a git commit message to a fixed standard - an imperative subject naming the edit, and a body only where a constraint demands one. Use when committing, amending or rewording a commit, splitting work into commits, or when a commit was rejected for not meeting the standard.
---

# Writing Commit Messages

## 1. Read the standard

Read `STANDARD.md` in this skill's directory. Read it - do not write from
memory of it.

## 2. Look at what you are about to commit

```bash
git diff --cached --stat
git diff --cached
```

The subject has to name what these lines do, and the diff is the only source
for it - not the task you were given, and not the message you are replacing
when you reword. This step is done when you can say what the diff does in one
line. Two unrelated things in the stat is two commits: stage them apart and
write a subject for each.

## 3. Draft the subject, then run it past every SUBJECT rule

One rule at a time, against the line you drafted. The two failures that get
committed most often are the ones that pass a mechanical check: reporting the
state after the change instead of naming the edit, and naming a goal for the
repository instead of the edit that reaches it.

## 4. Decide on a body

Most commits have no body. The gate is the standard's constraint test, and most
diffs do not meet it - then the subject is the whole message.

## 5. Commit

A body does not survive `-m` cleanly. Pass the message on stdin:

```bash
git commit -F - <<'EOF'
Subject line

Body line.
EOF
```
