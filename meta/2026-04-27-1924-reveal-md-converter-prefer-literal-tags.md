# Flip reveal-md-converter HTML escaping rule: prefer literal tags

- **When:** 2026-04-27 19:24
- **Who:** Maciej Małecki (via Claude Opus 4.7)
- **Asset type:** skill
- **Kind of change:** update

## Summary

Reversed the converter's stance on HTML inside fenced code blocks. The skill previously required `<` / `>` to be entity-escaped as `&lt;` / `&gt;` in any HTML/XML/JSX listing; it now tells the author to keep literal `<` / `>` and only escape the single token `</script>`, since that is the only sequence the browser parser reacts to inside `<script type="text/template">`.

## Purpose

After converting `09_angular.html` to its Markdown form, the user observed that the rendered deck looks correct even when the HTML code listings keep their original literal `<` / `>`. That matches what actually happens in the parser pipeline: inside `<script type="text/template">` the HTML tokenizer is in script-data state, so `<html>`, `<body>`, `<div>` etc. are read as plain text. The reveal.js Markdown plugin then hands the code-fence content to `marked`, which HTML-escapes `<` / `>` / `&` when rendering the `<pre><code>` block — so the listing displays correctly without author-side escaping.

The previous rule was over-broad: it generalized the genuine `</script>` hazard into "escape every angle bracket in HTML/JSX listings", which produced output that was harder to read and harder to round-trip from the source. The narrower rule keeps the same safety guarantee (the only thing that actually breaks the deck is a literal `</script>`) while letting authors copy listings verbatim. Considered keeping the strict rule as a "defensive default" but rejected it: the existing `08_*-md.html` decks already use literal characters in TypeScript generics and the `09_angular-md.html` output mixes both styles, so consistency with reality wins over defensiveness.

## Affected files

- `.claude/skills/reveal-md-converter/SKILL.md` — rewrote the "HTML *inside* fenced code blocks" section, replaced the worked example, added a focused subsection on the `</script>` exception, updated procedure step 5 (audit) and the two related edge cases to match.

## Originating prompt

> modify convert to md skill so that it preserves original encoding for html snippets in fenced boxes. It seems they are rendered properly

## Notes

- Supersedes the rule documented in `2026-04-26-2017-reveal-md-converter-html-escape-rule.md` — that entry remains as historical context for why the strict rule existed.
- The just-converted `09_angular-md.html` still uses entity-escaped HTML in code fences. Not back-porting it now; if it renders fine as-is, leave it. Future conversions and any rewrite of that deck should follow the new rule.
