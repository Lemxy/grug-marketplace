# grug

Lazy senior dev in a box. He writes one line, it works, he says little.

## Why: the Opus-at-Sonnet-price target

Claude API list pricing, per 1M tokens:

| Model | Input | Output |
|---|---|---|
| Claude Opus 4.8 | $5.00 | $25.00 |
| Claude Sonnet 5 | $3.00 | $15.00 |

Sonnet 5 is exactly **60%** of Opus 4.8 on both input and output. So the
break-even is a single number:

> **Cut token usage by 40% and Opus 4.8 costs what Sonnet 5 costs — at Opus
> quality.**

That is the target grug is built around.

```
Cost per equivalent task, Opus 4.8 = 100

Opus 4.8, no discipline  ████████████████████████████████████████  100%
Opus 4.8 − 20% tokens    ████████████████████████████████          80%
Opus 4.8 − 40% tokens    ████████████████████████                  60%  ← Sonnet 5 list price
Opus 4.8 − 50% tokens    ████████████████████                      50%
Sonnet 5, no discipline  ████████████████████████                  60%
                         └────┴────┴────┴────┴────┴────┴────┴────┘
                         0   20   40   60   80  100
```

**This chart is arithmetic, not a benchmark.** The 40% line is where the two
prices meet; it is not a measured claim that grug hits it on your codebase.
How far left you actually land depends on your work — measure it. The four
mechanisms below are where the reduction comes from.

## Where the tokens go

1. **Writes less code** — the laziness ladder (YAGNI → reuse → stdlib → native →
   existing dep → one line). Output tokens are the expensive ones ($25/1M vs
   $5/1M). Code never written is the cheapest code there is.
2. **Searches cheap** — grep/glob first, never blind file reads. Then the
   cheapest model that can answer: `grug-scout` on Haiku ($1/$5) for lookups,
   `grug-tracker` on Sonnet-low for tracing a flow. Work that would have run at
   Opus rates runs at a fifth of them.
3. **Keeps search noise out of the main thread** — subagents have their own
   context. Only their short answer comes back, so the main conversation (which
   you resend on every turn) stays lean.
4. **Parallelizes only independent work** — parallel subagents cost 4–7× one
   agent. grug fans out only when pieces share no state, and stays sequential
   on dependent chains.

Not cut, ever: validation at trust boundaries, error handling, security,
accessibility. Lazy, not negligent.

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

**Option A — from this marketplace:**

```
/plugin marketplace add Lemxy/grug-marketplace
/plugin install grug@grug-marketplace
```

(send the two commands as separate prompts)

**Option B — drop into a project:**

```bash
cp -r grug/skills/grug   .claude/skills/grug
cp    grug/agents/*.md   .claude/agents/
```

Restart Claude Code either way — agents load at session start.

## Measure it yourself

Do not take the chart on faith. Run a representative task twice — once with the
plugin installed, once with it removed — and compare `usage` totals. Input and
output are priced 5× apart, so weight them: `input×5 + output×25` per 1M.

## Tuning

- Force every subagent onto one model regardless of frontmatter:
  `export CLAUDE_CODE_SUBAGENT_MODEL=haiku`
- The skill is always-on by default. For on-demand only, narrow the
  `description:` in `SKILL.md` to a specific trigger phrase.
