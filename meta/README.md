# Meta change log

This folder records every change to the **Claude internal assets** of this repository — the configuration that shapes how Claude Code behaves when working here.

Tracked asset categories:

- **Skills** — `.claude/skills/**`
- **Agents** — `.claude/agents/**`
- **Slash commands** — `.claude/commands/**`
- **Settings + hooks** — `.claude/settings.json`, `.claude/settings.local.json`
- **Project rules** — `CLAUDE.md` (and any nested `CLAUDE.md` files)
- **Auto-memory** — files under the user-level memory directory referenced by this project
- **MCP configuration** — `.mcp.json` and `mcpServers` blocks in settings

Each meaningful change to one of those gets its own entry file in this folder, named `YYYY-MM-DD-HHMM-slug.md`. The entry records when, who, what, why, which files, and the originating prompt — see [`../templates/meta-entry.md`](../templates/meta-entry.md) for the template and field definitions.

The skill that maintains this log lives at [`.claude/skills/meta-changelog/SKILL.md`](../.claude/skills/meta-changelog/SKILL.md). It auto-triggers whenever Claude Code edits a tracked asset.

## Entries

<!-- Newest first. Group by date heading. Each entry: indented bullet linking to the file. -->

### 2026-04-28
- [12:02 — Auto-inject light-theme code-block override in reveal-md-converter](2026-04-28-1202-reveal-md-converter-light-theme-code-override.md)

### 2026-04-27
- [19:24 — Flip reveal-md-converter HTML escaping rule: prefer literal tags](2026-04-27-1924-reveal-md-converter-prefer-literal-tags.md)

### 2026-04-26
- [20:17 — Document HTML-in-code-fence escaping rule in reveal-md-converter](2026-04-26-2017-reveal-md-converter-html-escape-rule.md)
- [20:04 — Add reveal-md-converter skill](2026-04-26-2004-add-reveal-md-converter-skill.md)
- [19:57 — Add meta change-log skill](2026-04-26-1957-add-meta-changelog-skill.md)
