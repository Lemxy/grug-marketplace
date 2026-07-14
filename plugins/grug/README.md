# grug

Lazy senior dev in a box. He writes one line, it works, he says little.

Cuts tokens four ways:
1. **Writes less code** — the laziness ladder (YAGNI → reuse → stdlib → native → dep → one line).
2. **Searches cheap** — grep first, then the cheapest model that can answer (Haiku for lookups, Sonnet-low for understanding).
3. **Parallelizes only independent work** — no wasted subagents on dependent chains.
4. **Reports like a caveman** — short summaries, not essays.

## What's inside

```
grug/
├── .claude-plugin/plugin.json   # makes it an installable plugin
├── skills/grug/SKILL.md         # the brain: ladder + safety floor + search + report style
└── agents/
    ├── grug-scout.md            # Haiku, read-only, simple lookups
    └── grug-tracker.md          # Sonnet low-effort, read-only, deep flow tracing
```

## Install

**Option A — drop into a project** (fastest):

```bash
cp -r grug/skills/grug   .claude/skills/grug
cp    grug/agents/*.md   .claude/agents/
```

Restart Claude Code (agents load at session start). Done.

**Option B — as a plugin** (share across a team / all projects):

Push this folder to a git repo, then in Claude Code:

```
/plugin marketplace add <your-user>/grug
/plugin install grug@grug
```

(send the two commands as separate prompts)

## Tuning

- Force every subagent onto one model regardless of frontmatter:
  `export CLAUDE_CODE_SUBAGENT_MODEL=haiku`
- The skill is written to be always-on. If you only want it on demand, narrow
  the `description:` in `SKILL.md` to a specific trigger phrase.

## Notes

- Skills run in the main context; subagents run in their own. That split is why
  the search noise never pollutes your main thread.
- Parallel subagents cost ~4–7× the tokens of one. grug only parallelizes work
  with zero cross-dependency — otherwise it stays sequential.
- The safety floor (validation, error handling, security, accessibility) is
  never cut. Lazy, not negligent.
