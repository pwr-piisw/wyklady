---
name: reveal-md-converter
description: |
  Convert a reveal.js deck from pure-HTML slide markup (lots of <section> elements with HTML inside) into the Markdown-template form used by examples/cbm-deck.html — a single <section data-markdown data-separator-vertical="^--$"> containing a <script type="text/template"> with all slides as Markdown, separated by `---` (horizontal) and `--` (vertical). Triggers when the user asks to "convert this deck to markdown", "rewrite this slideshow as md", "port slides to data-markdown", or supplies a reveal.js HTML deck and asks for the cbm-deck-style version. Writes a sibling file (e.g. foo-md.html) and leaves the source untouched.
---

# Reveal.js HTML → Markdown deck converter

Convert a reveal.js deck whose slides are written as HTML `<section>` elements into the single-template Markdown form modeled by `examples/cbm-deck.html`.

The reference for "what the output should look like" is **`examples/cbm-deck.html`**. Read it before converting if you have not seen it recently — it is the ground truth for separators, class idioms, and the surrounding HTML scaffold.

## When to run this skill

- The user asks to convert, port, or rewrite a reveal.js deck to Markdown form.
- The user references `cbm-deck.html` and asks for "the same style" applied to another deck.
- The user pastes a reveal.js HTML file and asks for an `.md` or `data-markdown` version.

Do **not** run when:

- The deck is already in the Markdown-template form (single `<section data-markdown>` with a `<script type="text/template">`). In that case, point this out instead of producing a no-op rewrite.
- The user is asking for a thin shell that loads an external `.md` file. That's a different output shape; ask before proceeding.

## Output shape

One HTML file. Same `<head>` (preserve title, stylesheets, meta) and same closing `<script>` block (preserve `Reveal.initialize({...})` config). The body is collapsed to a single Markdown-template section:

```html
<div class="reveal">
  <div class="slides">
    <section data-markdown data-separator-vertical="^--$">
      <script type="text/template">
        <!-- all slides go here as Markdown, see separator rules below -->
      </script>
    </section>
  </div>
</div>
```

Note: `data-separator-vertical="^--$"` is set explicitly; the horizontal separator defaults to `^---?$` (matches `---`), so it does not need to be declared. Match `cbm-deck.html` verbatim on this attribute set.

### Separator rules

- `---` on its own line separates **horizontal** (top-level) slides.
- `--` on its own line separates **vertical** (sub-)slides within the current top-level slide.
- A horizontal separator implicitly closes any open vertical stack.

Map source structure as follows:

| Source                                                              | Output                                                       |
|---------------------------------------------------------------------|--------------------------------------------------------------|
| Top-level `<section>` containing only content                       | One Markdown block, separated from neighbors by `---`        |
| Top-level `<section>` containing nested `<section>`s                | Inner sections become vertical slides, separated by `--`     |
| Top-level `<section>` with content **and** nested `<section>`s      | Outer content becomes the first vertical slide, then `--` between nested sections |

## File handling

- Source file: leave untouched.
- Output file: write a sibling next to the source. If the source is `foo.html`, write `foo-md.html`. If `foo-md.html` already exists, append `-2`, `-3`, etc. — never overwrite without confirmation.
- After writing, report the output path and a one-line summary of what was converted (slide count, kept-as-HTML blocks count).

## Conversion rules

### Preserve verbatim

- Everything in `<head>` (title, charset, viewport, all `<link rel="stylesheet">`, theme links, custom CSS).
- The closing `<script src="dist/reveal.js">`, plugin scripts, and the `Reveal.initialize({...})` block. Even if the existing config differs from `cbm-deck.html` (different `width`, plugin list, etc.), keep the source's config — do not rewrite it.
- Any decorative absolute-positioned elements outside `.reveal` (e.g. `#cgLogo` in the lecture decks). Keep them where they sit in the body.

### Convert to Markdown

Convert these HTML constructs to Markdown when they appear as the entire content of a slide or a clean inline run:

- `<h1>`–`<h6>` → `#`–`######` (one space after the hashes).
- `<p>` containing only text and inline links → plain paragraph.
- `<a href="x">text</a>` → `[text](x)`.
- `<img src="x" alt="y">` → `![y](x)`. If the image has classes/sizing, keep them (see "Class preservation" below).
- `<ul><li>...</li></ul>` → `- ` bullets. `<ol>` → `1.` items. Nested lists indent by two spaces.
- `<strong>`/`<b>` → `**...**`, `<em>`/`<i>` → `*...*`, `<code>` → `` `...` ``.
- `<blockquote>` → `> ` lines.
- `<hr>` inside a slide → keep as `<hr>` (a Markdown `---` would be misread as a slide separator).

### Class preservation

When a converted Markdown element carries classes, attach them with reveal.js's element-attribute comment **on the same line**, separated by a space:

```markdown
## wciąż powstają gry<!-- .element class="pink"-->
![about](img/about.png)<!-- .element class="img-reduce"-->
```

Preserve class names exactly as the source uses them — do not rename or normalize. If the source uses `backlight`, `white`, `more-text`, `fragment`, `cyan`, etc., keep those strings.

Slide-level attributes (e.g. `data-background-image` on a `<section>`) become a slide-attribute comment on the **first line** of the slide:

```markdown
<!-- .slide: data-background-image="img/t-rex_64.png" data-background-opacity="0.3"-->
![T-Rex](img/t-rex_64.png)<!-- .element class="img-reduce"-->
```

### Keep as raw HTML

reveal.js's Markdown plugin allows raw HTML inside the template. Keep these as HTML rather than forcing a Markdown equivalent:

- Multi-column or flex layouts: `<div class="r-hstack">`, `<div class="col left">`, etc.
- Media: `<video>`, `<audio>`, `<iframe>`.
- `<cite>`, `<figure>`/`<figcaption>`, `<table>` with non-trivial structure.
- Fragments with custom data attributes: `<span class="fragment" data-fragment-index="2">...</span>`.
- Anything with inline `style="..."` that would lose meaning if stripped.

When keeping raw HTML, indent it consistently with the surrounding Markdown but do not wrap it in code fences.

### Author indentation inside the template

Indent every line of the Markdown template with one tab beyond the surrounding `<script type="text/template">`. The reveal.js Markdown plugin strips common leading whitespace, so consistent indentation matters but the depth itself does not. Match `cbm-deck.html`'s indentation style (tabs).

## Worked example

Source:

```html
<section>
  <h3>Plan zajęć</h3>
  <ol>
    <li>Architektura aplikacji webowej. Języki HTML5 i CSS3.</li>
    <li>Wprowadzenie do języków JavaScript oraz TypeScript.</li>
  </ol>
</section>
<section>
  <h3>Zasady zaliczeń</h3>
  <section>
    <p>Podstawą zaliczenia wykładu jest kolokwium.</p>
  </section>
  <section>
    <p>Zasady omówione na pierwszych zajęciach.</p>
  </section>
</section>
```

Output (inside the `<script type="text/template">`):

```markdown
### Plan zajęć

1. Architektura aplikacji webowej. Języki HTML5 i CSS3.
2. Wprowadzenie do języków JavaScript oraz TypeScript.
---
### Zasady zaliczeń
--
Podstawą zaliczenia wykładu jest kolokwium.
--
Zasady omówione na pierwszych zajęciach.
```

Note: the second top-level slide had nested sections, so its outer `<h3>` becomes the first vertical slide, then `--` separates the nested children.

## Procedure

1. **Read the source file** end-to-end. Note: lecture HTML files in this repo are in Polish — preserve the language, accents, and exact wording.
2. **Read `examples/cbm-deck.html`** if you do not already have it in context, to anchor on the target style.
3. **Plan the slide structure.** Walk the `<section>` tree and decide where each slide lands. Count: top-level slides, vertical sub-slides, slides that will keep raw HTML.
4. **Build the Markdown template** as a single string, slide by slide, applying the conversion and class-preservation rules above.
5. **Write the output file** as `<source-stem>-md.html`. Preserve the source's `<head>`, decorative absolute-positioned elements, and the `Reveal.initialize` block. Replace only the `<div class="reveal"><div class="slides">...</div></div>` body.
6. **Report** to the user: output path, slide counts (top-level, vertical, raw-HTML), and any constructs you intentionally kept as raw HTML so they can review.

## Edge cases

- **Empty `<section>`** (often used for spacing in source decks). Drop it; do not emit an empty slide.
- **`<section>` whose only child is an `<img>` with `data-background-image`-style attributes.** This is a background-image slide, not a content slide. Emit a slide-attribute comment on the first line and an empty body.
- **Slides containing custom JavaScript or `<script>` tags inside the slide body.** Keep as raw HTML and warn the user — these may interact badly with the Markdown plugin's parsing.
- **Source uses `data-markdown` already on some sections.** Those sections are already in Markdown; lift their template content directly without re-converting.
- **Source's `Reveal.initialize` plugin list differs from cbm-deck.** Keep the source's plugin list as-is; do not add `RevealMarkdown` if it is missing — instead, warn the user, because the converted deck will not render without it.
- **Mixed-language code blocks (e.g. ```` ```js ````).** Preserve fenced code blocks verbatim; do not unwrap them.
