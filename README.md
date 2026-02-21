# Cowork Plugins Marketplace

A [Claude Code](https://docs.claude.com/en/docs/claude-code/plugins) plugin marketplace for Claude Cowork: shared skills, commands, and extensions.

## Quick start

### Add this marketplace

From Claude Code (or a repo that uses this catalog):

```bash
# If this repo is cloned locally
/plugin marketplace add /path/to/cowork-plugins

# Or from GitHub (once pushed)
/plugin marketplace add https://github.com/your-org/cowork-plugins
```

### Install the example plugin

```bash
/plugin install example-plugin@cowork-plugins
```

Restart Claude Code after installing so the plugin is loaded.

### Use the example plugin

- **Example command** (slash command):  
  `/example-plugin:example` — greeting and short usage instructions.

- **Example skill**:  
  `/example-plugin:example-skill` — summarize the current file or selection.  
  The skill can also be invoked automatically when the user asks for a summary.

## Structure

```
cowork-plugins/
├── .claude-plugin/
│   └── marketplace.json    # Marketplace catalog
├── plugins/
│   └── example-plugin/     # Example plugin
│       ├── .claude-plugin/
│       │   └── plugin.json
│       ├── commands/
│       │   └── example.md
│       └── skills/
│           └── example-skill/
│               └── SKILL.md
└── README.md
```

## Adding plugins

1. Add a new directory under `plugins/`, e.g. `plugins/my-plugin/`.
2. Give it a manifest at `plugins/my-plugin/.claude-plugin/plugin.json`.
3. Add skills under `plugins/my-plugin/skills/<name>/SKILL.md` and/or commands under `plugins/my-plugin/commands/<name>.md`.
4. Register the plugin in `.claude-plugin/marketplace.json` under `plugins` with `name` and `source` (e.g. `"./plugins/my-plugin"`).

See [Create plugins](https://docs.claude.com/en/docs/claude-code/plugins) and [Plugin marketplaces](https://docs.claude.com/en/docs/claude-code/plugin-marketplaces) in the Claude Code docs for full details.
