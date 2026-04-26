# Add meta change-log skill

- **When:** 2026-04-26 19:57
- **Who:** Maciej Malecki (via Claude Opus 4.7)
- **Asset type:** skill
- **Kind of change:** add

## Summary

Introduced a project-level skill (`.claude/skills/meta-changelog/SKILL.md`) plus the supporting `meta/` change log and `templates/meta-entry.md`. From now on, any change to Claude internal assets in this repo gets a dated entry under `meta/`.

## Purpose

The repo already accumulates Claude configuration — a CLAUDE.md, a settings file, and now skills — but there has been no record of *why* any of it exists or how it has changed over time. Without that history, future-me (or another contributor) has to reverse-engineer intent from diffs, and subtle decisions (e.g. why a permission was added, why a hook fires only on certain paths) get lost.

A meta change log fixes that by forcing one short writeup per change, capturing the originating prompt and the deliberation. It is deliberately separate from git commit messages because:

1. Multiple Claude-asset changes can land in one commit alongside unrelated work.
2. The originating prompt is the most valuable artifact and is awkward to paste into a commit subject.
3. Browsing `meta/README.md` is faster than walking `git log` filtered by path.

Considered alternatives:

- **Settings hook (PostToolUse on Edit/Write).** More reliable than relying on Claude to self-trigger, but it would fire on every micro-edit, requires settings.json plumbing, and creates noisy entries. Rejected for now; can be added later if self-triggering proves unreliable.
- **Single rolling CHANGELOG.md.** Simpler, but the per-entry "Originating prompt" and "Purpose" sections get lost in a flat file, and merging concurrent edits is harder.
- **User-level skill.** Would apply to every repo, but each project's tracked assets and `meta/` location differ, so project-scoped is a better fit. Easy to promote later.

## Affected files

- `.claude/skills/meta-changelog/SKILL.md` — new skill definition; auto-trigger description, full instructions for writing entries, edge cases.
- `templates/meta-entry.md` — new entry template with placeholders and section guidance.
- `meta/README.md` — new index file describing the log's purpose, linking to the template and the skill, and ready to receive entries.
- `meta/2026-04-26-1957-add-meta-changelog-skill.md` — this entry; the first use of the skill is its own creation.

## Originating prompt

> Create new skill to implement claude management meta change log. this skill should handle changes in internal assets of claude code such as rules, agents, skills, commands and for each change log an entry in form of separate MD file describing date and time, who did it, summary, deliberate on purpose of changes, denote affected files, include original prompt users to do a change. log entry MD files should be stored in `meta` root folder. folder should contain an index file, namely README.md, describing purpose and enlisting click able links to the change log entries. Log entry should base on MD template file stored in `templates` root folder.

## Notes

Tracked asset categories were chosen via clarifying questions: skills/agents/commands, settings + hooks, CLAUDE.md + memory, and MCP configuration. Filename format is `YYYY-MM-DD-HHMM-slug.md` for chronological sorting plus same-day disambiguation. The skill auto-triggers via its description rather than via a settings.json hook — revisit if entries start being skipped.
