# Document HTML-in-code-fence escaping rule in reveal-md-converter

- **When:** 2026-04-26 20:17
- **Who:** Maciej Malecki (via Claude Opus 4.7)
- **Asset type:** skill
- **Kind of change:** update

## Summary

Added an explicit, mandatory rule to the `reveal-md-converter` skill: when a
fenced code block in the Markdown template shows HTML/XML/JSX source, every
`<` and `>` must be escaped to `&lt;` / `&gt;`. Added a matching audit step to
the procedure and expanded the "mixed-language code blocks" edge case.

## Purpose

The first run of the converter (on `08_typescript.html`) produced an output
where the raw-HTML code listings (e.g. the `<html><body><script>…</script>`
example slides) failed to render. Root cause: the deck's Markdown lives inside
`<script type="text/template">…</script>`, and the browser's HTML parser reads
that script's `textContent` *before* the reveal.js Markdown plugin runs.
Fenced ```` ```html ```` blocks are pure Markdown syntax — they do not
prevent the browser from interpreting the inner `<script>` / `</script>`
tokens, which truncates the template prematurely and eats the example tags.

The original source HTML deck stored these listings already entity-escaped
(`&lt;html&gt;`), but the conversion decoded them when copying into fenced
blocks. The skill needed an explicit instruction to preserve the escaping —
otherwise future conversions of any deck that teaches HTML will silently
break.

The skill now states the rule, gives a worked correct/incorrect example,
explains the reason (HTML parser runs first, eats `</script>`), and adds a
mandatory pre-write audit step. Also documents the `__SCRIPT_END__`
placeholder as an alternative the plugin supports, while recommending entity
escaping as the canonical approach (it is also what highlight.js needs).

## Affected files

- `.claude/skills/reveal-md-converter/SKILL.md` — added "HTML *inside* fenced code blocks (CRITICAL)" subsection under "Conversion rules"; expanded the mixed-language edge case; added a procedure step "Audit code fences for HTML content".
- `08_typescript-md.html` — fixed the two HTML example listings to use entity-escaped tags (separate from this skill change but the trigger for it).

## Originating prompt

> it does not seem that HTML sources are properly rendered, check how to fix
> it and change md conversion skill appropriately
>
> [follow-up clarification] I mean listing examples, when HTML is listed

## Notes

Only HTML/XML/JSX listings are affected. JS/TS/CSS/SQL/Java listings carry no
`<…>` tokens that the HTML parser would consume, so they remain literal.
