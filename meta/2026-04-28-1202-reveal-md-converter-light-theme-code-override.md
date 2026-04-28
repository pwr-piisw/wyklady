# Auto-inject light-theme code-block override in reveal-md-converter

- **When:** 2026-04-28 12:02
- **Who:** Maciej Małecki (via Claude Opus 4.7)
- **Asset type:** skill
- **Kind of change:** update

## Summary

Added a "Light-theme code-block override" section to the reveal-md-converter skill that tells the converter to drop a verbatim `<style>` block into the deck `<head>` when the slide theme is light. The block remaps monokai/zenburn dark code-block colors to a GitHub-light palette on white. Procedure step 6 was updated to mention this injection.

## Purpose

The bundled highlight stylesheets in this repo (`plugin/highlight/monokai.css`, `zenburn.css`) are both dark, and reveal.js doesn't ship a light highlight theme alongside them. Decks using a light slide theme like `dist/theme/white.css` therefore get dark code blocks that visually clash with the white slide background. After we hit this twice in quick succession (`08-2_typescript-md.html` and `09_angular-md.html`, both manually patched with the same inline `<style>` block right after `monokai.css`), the obvious next step was to fold the override into the converter so future conversions inherit it without having to ask.

Considered alternatives:

- **Add a real light-theme stylesheet** (e.g. ship `plugin/highlight/github-light.css`) and `<link>` it conditionally. Cleaner long-term, but requires either editing the vendored highlight plugin or adding a new file under `plugin/`, both of which expand the diff and would still require the converter to know which theme to pick. Punted as a follow-up.
- **Move the override into `css/custom.css`** so it applies project-wide. Tempting, but `custom.css` already loads in dark-themed decks (e.g. the `cbm-deck.html` reference uses `night.css`), and a global white-on-white code block override would break those. Per-file injection keeps the change scoped.
- **Conditional CSS via `prefers-color-scheme`.** Doesn't help — the slide theme choice is an explicit project decision, not a system preference.

The chosen approach (per-file `<style>` block, only injected for light slide themes) matches the pattern the user has already accepted twice and is reversible: the override sits in one named block in the `<head>`, easy to lift into a stylesheet later.

## Affected files

- `.claude/skills/reveal-md-converter/SKILL.md` — added a new "Light-theme code-block override" subsection under "Conversion rules" with the verbatim `<style>` block, the list of light vs. dark slide themes that gate the injection, and updated procedure step 6 to call out the inject.

## Originating prompt

> Update md conversion skill, so that white theme for snippets is applied automatically

## Notes

- Light slide themes flagged for injection: `white.css`, `simple.css`, `beige.css`, `solarized.css`, `serif.css`. Dark themes that should be left alone: `night.css`, `black.css`, `moon.css`, `blood.css`, `league.css`, `dracula.css`, `sky.css`.
- Existing markdown decks `08-2_typescript-md.html` and `09_angular-md.html` already carry the override inline (added by hand earlier this turn); no back-port needed.
- Follow-up worth considering: vendor a real `plugin/highlight/github-light.css` and have the skill `<link>` it instead of inlining a `<style>` block. Skipped now because the inline approach matches the precedent already in those two decks.
