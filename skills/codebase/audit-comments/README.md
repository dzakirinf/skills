# audit-comments

i remembered asking claude to move an icon to a different point because it was covering another icon, and it moved it well (obviously just chnage the x,y points) but then it also comments "Icon A is at <4,9> and not <6,7> because <6,7> will cover Icon B". WHAT THE FUCK?
and that's the one that I caught because it was a one-line change so i read the code. i'm pretty sure there's plenty of this in the codebase for changes that we don't read. so yeah so that was the background story of me making this skill.

this skill sorts every comment in scope into a keeper or a deletion, reports the deletions,
and names the keepers it protected so a later cleanup pass doesn't take them out.
it doesn't touch your files unless you want them touched (pause).

## Installing

```bash
npx skills add dzakirinf/skills --skill audit-comments
```

start a session and try `/audit-comments`.

this skill is only user-invocable and not model-invocable. you can change it in the skill itself.
below are some generated stuff that may or may not be useful.

## What it audits

Source the repo owns. Vendored dependencies, generated code and build output are
out of scope, and so is prose: `README` files, guides and changelogs have readers
of their own.

Templates and examples rank first. A changelog comment in a template gets pasted
into every downstream codebase, carrying ticket IDs its new readers can't open.

Inside that scope, files are ranked by comment-to-code ratio and read densest
first, but every comment in scope gets classified. The ranking sets reading order,
not coverage.

It is not a linter. Formatting, comment syntax and documentation coverage are
somebody else's job.

## Outputs

Findings grouped by file. Per finding: `file:line`, the comment, one line on what
makes it a deletion, and its replacement — either nothing, or the shorter comment
that survives. Then the keepers it protected, named, so a later pass doesn't
mistake a landmine for history. It closes on three counts: comment lines audited,
deleted outright, trimmed in place.

Nothing is written. Applying the findings is a separate pass you start yourself.

`SKILL.md` holds the classification rules — what makes a comment a changelog, what
makes it a landmine, and the three other shapes that go with the changelogs. This
README deliberately doesn't restate them, so the two can't fall out of sync.
