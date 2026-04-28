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

### Light-theme code-block override

The bundled highlight themes (`monokai.css`, `zenburn.css`) are both dark. When the slide theme is light (`dist/theme/white.css`, `simple.css`, `beige.css`, `solarized.css`, `serif.css`), dark code blocks clash with the surrounding page. Inject the following `<style>` block into the `<head>` **immediately after** the highlight stylesheet `<link>` so the override wins by source order. Do **not** apply it for dark slide themes (`night.css`, `black.css`, `moon.css`, `blood.css`, `league.css`, `dracula.css`, `sky.css` — anything where a dark code block is appropriate).

```html
<style>
  .reveal pre { box-shadow: none; background: #fff; }
  .reveal pre code,
  .reveal pre code.hljs { background: #fff; color: #24292e; }
  .reveal pre code .hljs-comment,
  .reveal pre code .hljs-quote,
  .reveal pre code .hljs-deletion,
  .reveal pre code .hljs-meta { color: #6a737d; }
  .reveal pre code .hljs-string,
  .reveal pre code .hljs-title,
  .reveal pre code .hljs-section,
  .reveal pre code .hljs-emphasis,
  .reveal pre code .hljs-type,
  .reveal pre code .hljs-built_in,
  .reveal pre code .hljs-builtin-name,
  .reveal pre code .hljs-selector-attr,
  .reveal pre code .hljs-selector-pseudo,
  .reveal pre code .hljs-addition,
  .reveal pre code .hljs-variable,
  .reveal pre code .hljs-template-tag,
  .reveal pre code .hljs-template-variable { color: #22863a; }
  .reveal pre code .hljs-attribute,
  .reveal pre code .hljs-symbol,
  .reveal pre code .hljs-regexp,
  .reveal pre code .hljs-link { color: #6f42c1; }
  .reveal pre code .hljs-code { color: #005cc5; }
  .reveal pre code .hljs-class .hljs-title { color: #24292e; }
</style>
```

Drop the block in verbatim — it remaps the monokai/zenburn tokens that read poorly on white (strings, comments, attributes, `hljs-code`) to GitHub-light equivalents while leaving keyword pink alone (already readable on white). Mention in the report that the override was added so the user can move it to `css/custom.css` if they want it project-wide.

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

### HTML *inside* fenced code blocks

Prefer **literal `<` / `>`** inside code fences. The deck's Markdown lives inside `<script type="text/template">…</script>`, where the browser parser is in script-data state — it does not tokenize tags, so `<html>`, `<body>`, `<div>`, etc. are read as plain text. The Markdown plugin then hands the code-fence content to `marked`, which HTML-escapes `<` / `>` / `&` when rendering the `<pre><code>` block, so the listing displays correctly.

Use literal characters because they read more naturally and round-trip cleanly. If the source already has `&lt;` / `&gt;` inside a `<pre><code>` (because it had to escape for raw-HTML rendering), **decode them back to literal `<` / `>`** in the Markdown output.

Worked example. Source:

```html
<pre class="html"><code class="hljs" data-trim>
  &lt;html&gt;
    &lt;body&gt;
      &lt;p&gt;hi&lt;/p&gt;
    &lt;/body&gt;
  &lt;/html&gt;
</code></pre>
```

Markdown output:

````markdown
```html
<html>
  <body>
    <p>hi</p>
  </body>
</html>
```
````

#### The one exception: `</script>`

There is exactly one sequence the browser parser still reacts to inside `<script type="text/template">`: the literal string `</script>`. It closes the surrounding template prematurely and truncates the entire deck.

If a code listing genuinely contains `</script>` (e.g. an HTML example with an inline script tag), escape **just that token** to `&lt;/script&gt;` — leave the other tags literal:

````markdown
```html
<html>
  <body>
    <script>console.log('hi');&lt;/script&gt;
  </body>
</html>
```
````

The reveal.js Markdown plugin also accepts the placeholder `__SCRIPT_END__` and rewrites it back to `</script>` after reading the template; entity-escaping is fine too. Either way, the rest of the listing stays as literal `<…>`.

This guidance applies to HTML, XML, and JSX listings. JavaScript, TypeScript, CSS, SQL, Java, etc. don't contain HTML tags and were never affected — write them with literal characters as normal. TypeScript generics like `Observable<User>` go in literally; `marked` escapes them for display.

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
5. **Audit code fences for `</script>`.** Before writing the file, search the template for the literal token `</script>`. If any code listing contains one, escape it to `&lt;/script&gt;` (or `__SCRIPT_END__`). Other `<…>` tags can stay literal — only `</script>` truncates the deck. This check is mandatory.
6. **Write the output file** as `<source-stem>-md.html`. Preserve the source's `<head>`, decorative absolute-positioned elements, and the `Reveal.initialize` block. Replace only the `<div class="reveal"><div class="slides">...</div></div>` body. If the source's slide theme is light (e.g. `white.css`), inject the light-theme code-block `<style>` override immediately after the highlight stylesheet — see "Light-theme code-block override" above.
7. **Report** to the user: output path, slide counts (top-level, vertical, raw-HTML), and any constructs you intentionally kept as raw HTML so they can review.

## Edge cases

- **Empty `<section>`** (often used for spacing in source decks). Drop it; do not emit an empty slide.
- **`<section>` whose only child is an `<img>` with `data-background-image`-style attributes.** This is a background-image slide, not a content slide. Emit a slide-attribute comment on the first line and an empty body.
- **Slides containing custom JavaScript or `<script>` tags inside the slide body.** Keep as raw HTML and warn the user — these may interact badly with the Markdown plugin's parsing.
- **Source uses `data-markdown` already on some sections.** Those sections are already in Markdown; lift their template content directly without re-converting.
- **Source's `Reveal.initialize` plugin list differs from cbm-deck.** Keep the source's plugin list as-is; do not add `RevealMarkdown` if it is missing — instead, warn the user, because the converted deck will not render without it.
- **Mixed-language code blocks (e.g. ```` ```js ````).** Preserve fenced code blocks verbatim; do not unwrap them. Inside fences, write tags with literal `<` / `>` — see "HTML *inside* fenced code blocks" above. The only token that must be escaped is `</script>`.
- **Slide containing a code listing that itself shows `</script>`.** The browser closes the outer template at the first literal `</script>` in the source. Escape that token to `&lt;/script&gt;` (or use the `__SCRIPT_END__` placeholder, which the Markdown plugin rewrites back). Other tags in the same listing can remain literal.
