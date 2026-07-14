# grug-marketplace

Personal marketplace that hosts the **grug** plugin so you can install it with
`/plugin install grug@grug-marketplace` instead of copying files by hand.

---

## What grug is for: the Opus-at-Sonnet-price target

grug is a lazy senior dev in a box — he writes one line, it works, he says
little. The point is token cost.

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
How far left you actually land depends on your work — measure it.

### Where the tokens go

1. **Writes less code** — the laziness ladder (YAGNI → reuse → stdlib → native →
   existing dep → one line). Output tokens are the expensive ones ($25/1M vs
   $5/1M). Code never written is the cheapest code there is.
2. **Searches cheap** — grep/glob first, never blind file reads. Then the
   cheapest model that can answer: `grug-scout` on Haiku ($1/$5) for lookups,
   `grug-tracker` on Sonnet-low for tracing a flow.
3. **Keeps search noise out of the main thread** — subagents have their own
   context. Only their short answer comes back, so the main conversation (which
   you resend on every turn) stays lean.
4. **Parallelizes only independent work** — parallel subagents cost 4–7× one
   agent. grug fans out only when pieces share no state.

Not cut, ever: validation at trust boundaries, error handling, security,
accessibility. Lazy, not negligent.

Full plugin docs: [`plugins/grug/README.md`](plugins/grug/README.md).

---

```
grug-marketplace/
├── .claude-plugin/
│   └── marketplace.json        # the catalog — lists "grug" and where it lives
└── plugins/
    └── grug/
        ├── .claude-plugin/plugin.json
        ├── skills/grug/SKILL.md
        ├── agents/grug-scout.md
        ├── agents/grug-tracker.md
        └── README.md
```

## Option A — local, no GitHub needed (fastest, good for testing)

From anywhere on your machine:

```bash
claude plugin marketplace add /absolute/path/to/grug-marketplace
claude plugin install grug@grug-marketplace
```

or inside a Claude Code session, as two separate messages:

```
/plugin marketplace add /absolute/path/to/grug-marketplace
/plugin install grug@grug-marketplace
```

Restart Claude Code afterward — subagents load at session start.

## Option B — GitHub, so it works from any machine / for your team

1. Push this folder as a repo, e.g. `github.com/<you>/grug-marketplace`.
2. In Claude Code (two separate prompts):

```
/plugin marketplace add <you>/grug-marketplace
/plugin install grug@grug-marketplace
```

Anyone with access to the repo can now install it the same way. To make it
load automatically for a team project, add to that project's
`.claude/settings.json`:

```json
{
  "extraKnownMarketplaces": {
    "grug-marketplace": {
      "source": { "source": "github", "repo": "<you>/grug-marketplace" }
    }
  }
}
```

## Updating

After you edit the plugin and push:

```
/plugin marketplace update grug-marketplace
```

## Uninstall

```
/plugin remove grug
/plugin marketplace remove grug-marketplace
```

## Notes

- `marketplace.json` and `plugin.json` are two different files with different
  jobs: `marketplace.json` is the catalog ("here's what plugins exist and
  where"), `plugin.json` is the plugin's own metadata ("here's what I am").
  Both are required — one without the other won't install via `/plugin`.
- Before renaming things, fill in real `owner`/`author` name+email in both
  JSON files — Claude Code doesn't require it to be accurate, but it's good
  practice for anything you share.
