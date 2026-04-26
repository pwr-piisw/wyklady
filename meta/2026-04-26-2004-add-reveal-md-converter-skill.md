# Add reveal-md-converter skill

- **When:** 2026-04-26 20:04
- **Who:** Maciej Malecki (via Claude Opus 4.7)
- **Asset type:** skill
- **Kind of change:** add

## Summary

Added a project-level skill at `.claude/skills/reveal-md-converter/SKILL.md` that converts reveal.js decks from pure-HTML slide markup into the single-template Markdown form modeled by `examples/cbm-deck.html`.

## Purpose

Most lecture decks in this repo (e.g. `01_wprowadzenie_html_css.html`, `09_angular.html`) are written as pages of HTML `<section>` elements with hand-formatted lists, headings, and images. `examples/cbm-deck.html` demonstrates a much more compact authoring style: a single `<section data-markdown>` whose `<script type="text/template">` block holds the entire deck as Markdown, with `---` and `--` as horizontal/vertical slide separators. Editing slides as Markdown is significantly faster and produces cleaner diffs.

The skill exists so that ports of existing decks to this style are mechanical and consistent — the user does not have to rediscover the conventions (tab indentation inside the template, element-attribute comments for class preservation, slide-attribute comments for background images, `data-separator-vertical="^--$"` only) every time.

Decisions locked in by clarifying questions, recorded here so the skill's choices are reviewable later:

- **Output shape: single HTML file with inline Markdown.** Match `cbm-deck.html` exactly. The alternative — a thin HTML shell loading an external `.md` — is cleaner for editing but creates a two-file invariant the lecture decks do not currently have. The user can ask for that variant explicitly.
- **Style handling: preserve classes verbatim.** Lecture decks use idioms like `backlight`, `more-text`, `col left` that have no equivalent in `cbm-deck.html`'s vocabulary (`cyan`, `pink`, `r-fit-text`, `img-reduce`). Renaming would silently change visual meaning, so the skill keeps source class names as-is and emits them via `<!-- .element class="..."-->` comments.
- **Fallback: keep raw HTML inline.** reveal.js's Markdown plugin allows HTML inside the template, so multi-column flex layouts, `<video>`, `<cite>`, fragments with data attributes, etc. are kept as HTML rather than forced through a lossy Markdown approximation.
- **File handling: sibling output, source untouched.** Writes `foo-md.html` next to `foo.html`. Lets the user diff and replace at their own pace; avoids destructive in-place rewrites.

## Affected files

- `.claude/skills/reveal-md-converter/SKILL.md` — new skill definition with auto-trigger description, conversion rules (separator mapping, class preservation, raw-HTML fallback list), procedure, worked example, and edge cases.

## Originating prompt

> Create new skill that converts RevealJS decks from pure HTML format into MD version. There is an example of proper MD version deck in @examples/cbm-deck.html

## Notes

`examples/cbm-deck.html` was supplied via a `<system-reminder>` Read result rather than being committed to the repo at the time of writing — it is an untracked file in the working tree. The skill instructs Claude to read it before converting; if it gets removed or relocated later, update the skill's "reference for what the output should look like" pointer.
