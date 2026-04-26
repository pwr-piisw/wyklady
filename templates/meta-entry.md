<!--
Template for meta change-log entries.
Copy this file into meta/ as YYYY-MM-DD-HHMM-slug.md, fill in every section,
and add an indented bullet under the matching date heading in meta/README.md.

Required values:
  {{TITLE}}              short title, sentence case
  {{DATE_TIME}}          ISO 8601 local time, e.g. 2026-04-26 14:30
  {{AUTHOR}}             git user.name (or "user" + Claude model id)
  {{ASSET_TYPE}}         one of: skill | agent | command | settings | hook
                                 | CLAUDE.md | memory | mcp
  {{CHANGE_KIND}}        one of: add | update | remove
-->
# {{TITLE}}

- **When:** {{DATE_TIME}}
- **Who:** {{AUTHOR}}
- **Asset type:** {{ASSET_TYPE}}
- **Kind of change:** {{CHANGE_KIND}}

## Summary

One or two sentences describing what changed at the surface level.

## Purpose

Why this change was made. What problem it solves, what behavior it unlocks
or removes, and what alternatives were considered. This is the section a
future reader will care about most — be specific about *why*, not *what*.

## Affected files

- `path/to/file` — short note on what changed in this file
- `path/to/another/file` — ...

## Originating prompt

The user request that triggered this change, quoted verbatim. If the change
spanned multiple turns, include the prompt that initiated it and note any
follow-ups inline.

> paste user prompt here

## Notes

Anything else worth recording: links to related entries, follow-up work,
caveats, or decisions explicitly deferred.
