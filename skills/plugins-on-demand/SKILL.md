---
name: plugins-on-demand
description: Keep Claude Code plugins disabled until a task needs them, then enable the right one and disable it again afterwards. Use when the user asks to enable, disable, list, or clean up plugins; when they say their sessions are slow or their context is full because of plugins; when a task is specialized (SEO, social media, design, video, finance, a domain-specific workflow) and an installed-but-disabled plugin might cover it; or when the user's CLAUDE.md plugin index points to a disabled plugin for the task at hand.
---

# Plugins on demand

Every enabled plugin adds its skill, agent, and command descriptions to **every** session, whether or not they are used. A few large plugins can add tens of thousands of tokens. This skill keeps plugins off by default and turns them on only for the task that needs them.

## Find the CLI

Use `claude` if it is on `PATH`. Otherwise try `~/.local/bin/claude` (macOS/Linux) or `~/.local/bin/claude.exe` (Windows). If none works, ask the user where Claude Code is installed. Below, `claude` means whichever one you found.

## State files

Keep state in `~/.claude/plugins-on-demand/`:

- `catalog.md`: one row per installed plugin (id, one-line purpose, always-on tokens, whether it ships MCP servers).
- `pinned.txt`: plugin ids the user wants always on, one per line. Never disable these.
- `enabled-by-me.txt`: plugin ids this skill enabled and has not yet disabled.

Create the folder and files if they are missing.

## Build or refresh the catalog

Rebuild `catalog.md` when it is missing, or when `claude plugin list --json` shows ids that are not in it.

1. `claude plugin list --json` returns an array of `{ id, enabled, scope, installPath, ... }`.
2. For each id, `claude plugin details <id>` prints the description, the component inventory (skills, agents, hooks, MCP servers), and an `Always-on: ~N tok` line.
3. Write one short row per plugin. Keep the purpose under 15 words.

Skip this plugin's own id (`plugins-on-demand@...`).

## First-time setup

Run this once, when the user first asks for on-demand plugins or `catalog.md` does not exist yet.

1. Build the catalog and show it to the user as a short table sorted by always-on tokens.
2. Ask which plugins, if any, to pin as always on. Save the answer to `pinned.txt`.
3. Ask before disabling the rest. Then, for each unpinned enabled plugin, run `claude plugin disable <id> --scope <scope>`, using the scope from the JSON.
4. Offer to add a short index to `~/.claude/CLAUDE.md` so future sessions know when to call this skill. Write it only if the user agrees. Keep it under 15 lines:

   ```markdown
   ## Disabled plugins (enable with the plugins-on-demand skill)
   - seo-toolkit@my-marketplace: SEO audits, keywords, schema markup
   - social-media@my-marketplace: social posts, scripts, captions, platform growth
   ```

   Update this block whenever the catalog changes.

## Enable for a task

1. Read `catalog.md` and pick the **smallest** set of plugins that covers the task, usually one.
2. If the plugin ships MCP servers, tell the user it may ask them to sign in to an external service. Ask before enabling it.
3. Run `claude plugin enable <id> --scope <scope>` and append the id to `enabled-by-me.txt`.
4. Plugins load when a session starts. Tell the user in one line to start a new session for this task. If they want to continue now, read the plugin's skill files directly from its `installPath` (`<installPath>/skills/<skill>/SKILL.md`) and follow them by hand.

## Disable after use

When the task that needed a plugin is done, or the user moves to unrelated work:

1. For each id in `enabled-by-me.txt` that is not in `pinned.txt`, run `claude plugin disable <id> --scope <scope>`.
2. Remove those ids from `enabled-by-me.txt`.
3. Say in one line which plugins you turned off. The change applies to the next session.

## Rules

- Never install, uninstall, or update plugins unless the user asks. This skill only enables and disables.
- Never disable a pinned plugin, and never disable this plugin.
- Never enable several plugins "just in case". Prefer one, and say which one and why.
- If `claude plugin enable` or `disable` fails, show the error. Do not edit `settings.json` by hand to work around it.
