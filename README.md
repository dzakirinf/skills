# skills

These skills exist to deslop.

Folders under `skills/` are named for the surface a skill reads and writes (the
knowledge base, the work-tracking record, the codebase) rather than the tool it
drives, so there's somewhere obvious to put the next one.

## Installing

### Claude Code

The repo is its own plugin marketplace. Add it once:

```
/plugin marketplace add dzakirinf/skills
```

Then install a group:

```
/plugin install work-tracking@dzakirinf
```

| Plugin | Covers |
| --- | --- |
| `work-tracking@dzakirinf` | `writing-commit-messages`, `writing-pr-bodies`, `ticket-drift`, `weekly-recap` |
| `concept-docs@dzakirinf` | `creating-confluence-clusters`, `revising-confluence-clusters`, `writing-confluence-pages` |
| `prose@dzakirinf` | `copyedit` |
| `codebase@dzakirinf` | `audit-comments` |
| `dzakirin-skills@dzakirinf` | The other four as dependencies, so one install takes everything. |

`/plugin` on its own opens a menu for browsing and toggling installed plugins. The same
two commands work from a shell as `claude plugin marketplace add dzakirinf/skills` and
`claude plugin install work-tracking@dzakirinf`.

### Everything else

```bash
npx skills add dzakirinf/skills
```

That lists what's here to pick from. To take one directly:

```bash
npx skills add dzakirinf/skills --skill ticket-drift
```

[`npx skills`](https://github.com/vercel-labs/skills) installs into whichever agents
are present, Codex and OpenCode included. Start a new session afterwards, since agents
only look for skills at startup.

## What's here

| Skill | | Needs |
| --- | --- | --- |
| [`audit-comments`](skills/codebase/audit-comments/) | Finds the comments that tell a reader nothing the code already does, and reports them for removal. | Nothing |
| [`concept-docs`](skills/knowledge-base/concept-docs/) | Authors and maintains Confluence documentation clusters. Three skills; take all three or none. | Atlassian MCP server |
| [`copyedit`](skills/prose/copyedit/) | Rewrites prose to a plain register: strips AI tells, decoration and stiffness, and moves the point to the front. | Nothing |
| [`ticket-drift`](skills/work-tracking/ticket-drift/) | Finds the backlog tickets that finished work invalidated, and comments on each. | [`acli`](https://developer.atlassian.com/cloud/acli/) |
| [`weekly-recap`](skills/work-tracking/weekly-recap/) | Rebuilds last week from GitHub into a dated file, facts in tables and reasoning in prose. | [`gh`](https://cli.github.com/) |
| [`writing-commit-messages`](skills/work-tracking/writing-commit-messages/) | Writes a commit message to a fixed standard: an imperative subject naming the edit, a body only where a constraint demands one. | Nothing |
| [`writing-pr-bodies`](skills/work-tracking/writing-pr-bodies/) | Writes a PR title and body to a fixed standard: a ticket-keyed title, then diagrams, code and tables connected by bullets. | [`gh`](https://cli.github.com/) |

Where a skill has its own README, it covers what the skill writes and what it
leaves alone.

## Sanitized identifiers

This ran against a private Jira / Confluence instance and a private set of repos
first, so the worked example names a project, tickets and services that don't exist.
I changed the identifiers and nothing else. Where a number does real work, it's the
true one.

## License

MIT. See [LICENSE](LICENSE).
