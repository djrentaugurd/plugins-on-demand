# plugins-on-demand

*[Leer en español](README.es.md)*

A Claude Code plugin that keeps your other plugins **disabled until a task needs them**, then turns the right one on and switches it off again when the task is done.

## Why

Every enabled plugin adds the descriptions of its skills, agents, and commands to **every** session, whether you use them or not. Install a few large skill packs and you can lose tens of thousands of tokens of context before you type anything. That makes sessions cost more and makes it harder for Claude to pick the right skill.

`claude plugin details <plugin>` shows the cost of each plugin on the `Always-on:` line.

## What it does

- **Catalogs** your installed plugins: what each one is for, how many tokens it costs, and whether it ships MCP servers that need a sign-in.
- **Disables** everything you don't pin as always on, after asking you.
- **Enables** the smallest plugin that fits when a task needs one, and tells you to start a new session so it loads.
- **Disables** it again when the task is done.
- Optionally writes a short index to `~/.claude/CLAUDE.md`, so future sessions know which disabled plugin covers which kind of task.

It only enables and disables plugins. It never installs, uninstalls, or updates them.

## Install

From inside Claude Code:

```
/plugin marketplace add djrentaugurd/plugins-on-demand
/plugin install plugins-on-demand@plugins-on-demand
```

Or from a terminal:

```bash
claude plugin marketplace add djrentaugurd/plugins-on-demand
claude plugin install plugins-on-demand@plugins-on-demand
```

## Use

Start a new session and say something like:

> Set up plugins on demand.

Claude builds the catalog, shows you what each plugin costs, asks which ones to keep always on, and disables the rest. After that, just work normally. When a task needs a disabled plugin, Claude enables it and asks you to open a new session. You can also ask directly:

> Enable the SEO plugin. / Turn off the plugins you enabled. / Which plugins are on?

## Limitations

- **Enabling takes effect in the next session.** Claude Code loads plugins when a session starts. If you don't want to restart, Claude can read the plugin's skill files directly and follow them in the current session.
- **Claude only knows about disabled plugins it has been told about.** The optional `CLAUDE.md` index is what lets a future session realize a disabled plugin fits the task. Without it, ask for the plugin by name or topic.
- **Plugins with MCP servers may ask you to sign in** to an external service once enabled. The skill warns you before enabling one.

## State

The skill keeps its state in `~/.claude/plugins-on-demand/`:

| File | Contents |
|---|---|
| `catalog.md` | One row per installed plugin: id, purpose, always-on tokens, MCP servers |
| `pinned.txt` | Plugins that stay always on |
| `enabled-by-me.txt` | Plugins it enabled and has not turned off yet |

These are plain text files, so you can edit them by hand. Delete the folder to start over.

## Requirements

- Claude Code with the `claude plugin` command (`enable`, `disable`, `list --json`, `details`).
- Works on Windows, macOS, and Linux. It looks for `claude` on `PATH`, then in `~/.local/bin/`.

## License

[MIT](LICENSE)
