# skills

The primary motivation for writing these skills is to deslop.

Folders under `skills/` are named for the tool the skill drives, so there's
somewhere obvious to put the next one.

## Installing

```bash
npx skills add dzakirinff/skills
```

That lists what's here and lets you pick. To take one directly:

```bash
npx skills add dzakirinff/skills --skill ticket-drift
```

[`npx skills`](https://github.com/vercel-labs/skills) installs into whichever
agents you have, Codex and OpenCode included, so these work outside Claude Code even
though that's where I wrote them. Start a new session afterwards, since agents only
look for skills at startup.

## skills/jira/

**[`ticket-drift`](skills/jira/ticket-drift/)** finds the backlog tickets your
finished work just invalidated. It leaves a comment on each explaining what moved,
and a dated pointer on the description so that whoever picks the ticket up reads
the comment before starting rather than after.

It needs [`acli`](https://developer.atlassian.com/cloud/acli/) authenticated
against your Jira site. It will use the Atlassian MCP server too if you have one
connected, but doesn't need it. Its own
[README](skills/jira/ticket-drift/) covers what it writes and what it deliberately
leaves alone.

## On the sanitizing

This ran against a private Jira / Confluence instance and a private set of repos before it ran
anywhere else, so the worked example names a project, tickets and services that
don't exist.

I changed the identifiers and nothing else. The mechanics, the measurements and the
failure modes are what I actually observed. Where a number does real work, like a
page size or a row count or a timing, it's the true one, because the number is
usually the whole reason the rule exists.

## License

MIT. See [LICENSE](LICENSE).
