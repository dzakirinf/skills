# skills

The primary motivation for writing these skills is to deslop.

Folders under `skills/` are named for the surface a skill reads and writes — the
knowledge base, the work-tracking record, the codebase — rather than the tool it
drives, so there's somewhere obvious to put the next one.

## Installing

```bash
npx skills add dzakirinff/skills
```

That lists what's here and lets you pick. To take one directly:

```bash
npx skills add dzakirinff/skills --skill ticket-drift
```

[`npx skills`](https://github.com/vercel-labs/skills) installs into whichever agents
you have, Codex and OpenCode included. Start a new session afterwards, since agents
only look for skills at startup.

## What's here

| Skill | | Needs |
| --- | --- | --- |
| [`concept-docs`](skills/knowledge-base/concept-docs/) | Authors and maintains Confluence documentation clusters. Three skills; take all three or none. | Atlassian MCP server |
| [`ticket-drift`](skills/work-tracking/ticket-drift/) | Finds the backlog tickets your finished work just invalidated, and comments on each. | [`acli`](https://developer.atlassian.com/cloud/acli/) |
| [`weekly-recap`](skills/work-tracking/weekly-recap/) | Rebuilds last week from GitHub into a dated file, facts in tables and reasoning in prose. | [`gh`](https://cli.github.com/) |

Each has its own README covering what it writes and what it leaves alone.

## On the sanitizing

This ran against a private Jira / Confluence instance and a private set of repos
first, so the worked example names a project, tickets and services that don't exist.
I changed the identifiers and nothing else — where a number does real work, it's the
true one.

## License

MIT. See [LICENSE](LICENSE).
