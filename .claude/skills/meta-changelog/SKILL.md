---
name: meta-changelog
description: |
  Record a meta change-log entry whenever Claude internal assets in this repo are added, modified, or removed. Triggers on any edit to: .claude/skills/**, .claude/agents/**, .claude/commands/**, .claude/settings.json, .claude/settings.local.json, .mcp.json, CLAUDE.md (root or nested), or auto-memory files for this project. Also triggers on direct user requests like "log this meta change", "add a meta entry", or "/meta-log". Writes one entry per logical change to meta/<YYYY-MM-DD-HHMM-slug>.md using templates/meta-entry.md, and updates meta/README.md so the new entry appears in the index.
---

# Meta change-log skill

You maintain a change log of every modification to this repository's **Claude internal assets** — the files that configure how Claude Code behaves here.

## When to run this skill

Trigger this skill **after** you finish a logical unit of work that added, modified, or removed any of the following in this repo:

| Category | Paths |
| --- | --- |
| Skills | `.claude/skills/**` |
| Agents | `.claude/agents/**` |
| Slash commands | `.claude/commands/**` |
| Settings + hooks | `.claude/settings.json`, `.claude/settings.local.json` |
| MCP configuration | `.mcp.json`, `mcpServers` blocks inside settings files |
| Project rules | `CLAUDE.md` (root or any nested copy) |
| Auto-memory | Files in the user-level `memory/` directory associated with this project |

Run the skill **once per logical change**, not once per file edit. If a single user request results in editing three files of one skill, that's one meta entry, not three. If the same turn changes both a skill and a slash command for unrelated reasons, that's two entries.

**Do not run** when:

- The change is to lecture content, build output, or anything outside the categories above.
- You're only reading the files, not modifying them.
- A user explicitly asks you not to log the change.
- The user is reverting a change you already logged in this same turn (in that case, delete the just-written entry and its index line instead).

## How to write an entry

### 1. Gather the facts

Before writing, collect:

- **Current local date and time.** Run `date "+%Y-%m-%d %H:%M"` (Bash) or check the `currentDate` context. Use 24-hour time. The filename uses `HHMM` (no colon); the body uses `HH:MM`.
- **Author identity.** Run `git config user.name` for the human author. The agent identity is `Claude` plus the model id from the environment header (e.g. `Claude Opus 4.7`). Format the `Who` field as `<git user.name> (via <Claude model>)`.
- **Affected files.** The exact paths you edited or created in this logical change, relative to the repo root.
- **Originating prompt.** The user message that initiated the change. Quote it verbatim — do not paraphrase. If the work spanned multiple user turns, quote the prompt that started it and add a one-line note for any follow-up turns that materially shaped the result.
- **Slug.** A short, kebab-case summary of the change (e.g. `add-meta-changelog-skill`, `tighten-eslint-rules`, `remove-unused-agent`). Keep it under ~50 characters.

### 2. Create the entry file

1. Read `templates/meta-entry.md` to get the current template.
2. Write a new file at `meta/<YYYY-MM-DD>-<HHMM>-<slug>.md` whose contents are the template with every `{{PLACEHOLDER}}` filled in and every section body completed.
3. Fill the sections honestly:
   - **Summary** — one or two sentences. What changed, surface level.
   - **Purpose** — why. The constraint, request, or insight that motivated it. What alternatives were considered or rejected. This is the load-bearing section; do not skimp.
   - **Affected files** — every path that was created, modified, or deleted as part of this change. One bullet per file with a short note on what changed in it.
   - **Originating prompt** — the verbatim user prompt as a Markdown blockquote.
   - **Notes** — optional. Use it for cross-references, deferred follow-ups, or known caveats. Omit the section entirely if there's nothing to say.
4. Remove the leading HTML comment block from the template (it's instructions for the author, not part of the entry).

### 3. Update the index

Edit `meta/README.md` to add a link to the new entry under the **Entries** heading:

- If a heading for today's date (`### YYYY-MM-DD`) already exists, add a bullet underneath it.
- Otherwise insert a new date heading **above** the existing entries (newest first) and add the bullet there.
- Bullet format: `- [HH:MM — <Title>](<filename>.md)` — the title should match the entry's H1.
- If the placeholder `_(no entries yet)_` is still present, replace it with the new heading + bullet.

### 4. Verify

Before considering the skill done:

- The entry filename matches `^\d{4}-\d{2}-\d{2}-\d{4}-[a-z0-9-]+\.md$`.
- Every section in the template has real content (no `{{...}}` placeholders left over).
- `meta/README.md` contains exactly one new line linking to the entry, and the link resolves.
- The originating prompt is a blockquote and appears verbatim.

## Examples

### Filename

```
meta/2026-04-26-1430-add-meta-changelog-skill.md
```

### Index bullet

```markdown
### 2026-04-26
- [14:30 — Add meta change-log skill](2026-04-26-1430-add-meta-changelog-skill.md)
```

### Who field

```
- **Who:** Maciej Malecki (via Claude Opus 4.7)
```

## Edge cases

- **Multiple unrelated changes in one turn.** Write one entry per logical change, each with its own slug. They can share a date+time prefix if they happened in the same minute — disambiguate via the slug.
- **Change is later reverted in the same turn.** Delete the entry file and its index line rather than logging the revert as a second entry.
- **User asks to backfill an older change.** Use the change's actual date/time, not "now". Insert the entry under the correct date heading in the index.
- **Bootstrapping.** If `meta/` or `templates/` doesn't exist, create them and seed `meta/README.md` and `templates/meta-entry.md` from the structure described above before writing the first entry.
