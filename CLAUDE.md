# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repo is

University lecture slides for "Projekt i implementacja systemów webowych" (PWR). The presentation framework is **reveal.js 4.1.0** (vendored, not consumed as an npm dep) — this repo contains both the framework source under `js/`, `css/`, `plugin/`, `dist/` and the actual lecture content as top-level HTML files.

Lecture content is in **Polish**. Preserve language when editing lecture HTML.

## Lecture entry points

`index.html` is the landing page that links to each lecture. The numbered HTML files at the repo root (`01_wprowadzenie_html_css.html`, `02_javascript_typescript.html`, `03-backend.html`, `04-persistence.html`, `05_typescript.html` / `08_typescript.html`, `08_frontend-testing.html`, `09_angular.html`, `10_reactive.html`, …) are individual reveal.js presentations. Each is a self-contained HTML doc that pulls in `dist/reveal.js`, `dist/reveal.css`, a theme from `dist/theme/`, plugins from `plugin/*`, and overrides from `css/custom.css`.

When adding a new lecture, link it from `index.html` and follow the structure of an existing file (same `<head>` block, `<div class="reveal"><div class="slides">…</div></div>`, same `Reveal.initialize({ width: 1200, hash: true, navigationMode: 'linear', slideNumber: 'c/t', plugins: [...] })` block).

Diagrams live in `diagrams/` (drawio `.xml`/`.drawio` and PlantUML `.puml`). Lecture outlines / notes are in `konspekt/` as Markdown. `examples/` ships with reveal.js and is reference-only.

## Build / serve / test

Node ≥10. Build pipeline is gulp + rollup (NOT npm scripts beyond thin wrappers).

```
npm install
npm start          # gulp serve — dev server on :8000 with livereload + watchers
npm run build      # gulp — full build (js + css + plugins + test)
npx gulp build     # build only, no test
npm test           # gulp test = eslint + qunit (puppeteer)
npx gulp js        # rebuild JS bundles only (dist/reveal.js + dist/reveal.esm.js)
npx gulp css       # rebuild CSS only
npx gulp plugins   # rebuild plugin bundles only
npx gulp eslint    # lint js/** and gulpfile.js
npx gulp qunit     # run QUnit tests through puppeteer
```

`gulp serve` watches: `*.html` and `*.md` (reload), `js/**` (rebuild + reload + test), `plugin/**/plugin.js` (rebuild plugins + reload), `css/theme/source/*.{sass,scss}` (rebuild themes), `css/*.scss` and `css/print/*` (rebuild core css), `test/*.html` (re-run tests).

When editing lecture HTML only, `npm start` is enough — no rebuild needed.

## Build outputs are committed

`dist/reveal.js`, `dist/reveal.esm.js`, `dist/reveal.css`, `dist/theme/*` and the `plugin/*/highlight.js`-style bundle outputs are checked in. After modifying `js/**`, `css/**`, or `plugin/**/plugin.js`, rebuild and commit the resulting `dist/` and `plugin/*` bundle files together with the source change — `index.html` and the lecture files load from `dist/` and `plugin/*/*.js` directly.

## Code style (from CONTRIBUTING.md)

- Tabs for indentation
- Single-quoted strings
- ESLint config in `package.json` (`eqeqeq: 2`, `no-eq-null: 2`, `new-cap: 2`, etc.) — `gulp eslint` is the source of truth

## Things to know

- `bower.json` and `Gruntfile.js` are present but vestigial; the active toolchain is gulp + rollup as configured in `gulpfile.js`.
- Don't open lecture HTML directly via `file://` — reveal.js plugins and some assets need an HTTP origin. Use `npm start`.
- The `CONTRIBUTING.md` and upstream guidance about "submit PRs to dev branch" applies to upstream reveal.js, not this lecture fork — this repo's default branch is `master`.
