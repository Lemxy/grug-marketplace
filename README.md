# grug-marketplace

Personal marketplace that hosts the **grug** plugin so you can install it with
`/plugin install grug@grug-marketplace` instead of copying files by hand.

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
