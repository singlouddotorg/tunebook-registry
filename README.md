# Tunebook Registry

The published shape-note tunebook data the Sing Loud Suite reads — every Work and Edition,
their page and title indexes, and the Level 3 scholarly files — browsable through this
repository's own `index.html`, and read directly by every other app in the suite.

This repository is the **source of truth** for that data. The apps carry copies; this is the
one they are copies of. Unlike the other three, it was maintained directly on GitHub until
2026-09-06d, when its front page was brought into the shared working tree alongside them, so
it now builds and ships from one source like the rest of the suite rather than being edited
in place.

## Part of the Sing Loud Suite

| App | What it does |
|---|---|
| [**Minutes**](https://github.com/singlouddotorg/minutes) | Log a singing as it happens, then turn that log into publishable minutes. |
| [**Tunebooks**](https://github.com/singlouddotorg/tunebooks) | Curate the shared tunebook data — editions, page indexes, Level 3 scholarly files. |
| [**Simple Minutes**](https://github.com/singlouddotorg/simple-minutes) | A phone-sized logger: page numbers only, no names. Its files import straight into Minutes. |
| [**Tunebook Registry**](https://github.com/singlouddotorg/tunebook-registry) | The published tunebook data the others read. |

## How data gets here

```
edited in Tunebooks  →  exported as tunebook-library.js  →  published here  →  read by the apps
```

[Tunebooks](https://github.com/singlouddotorg/tunebooks) is the editor. This repository is where its output is
published. Simple Minutes fetches `tunebook-library.js` directly from here at runtime;
Minutes and Tunebooks each carry a copy alongside the app.

## What's here

| File | What it is |
|---|---|
| `index.html` | Browsable index of every tunebook this project knows about, by level. Open this directly, or visit the repo's GitHub Pages site. |
| `codes.html` | FAQ on the various code systems used for the same books across different sources. |
| `shared-utils.js` | Utilities this front page reads from (CSV parsing, page sorting, title building) — the same file Minutes and Tunebooks carry, generated from one source so it can't drift. |
| `tunebook-library.js` | The library: every Work and Edition, with page/title indexes for those that have them. |
| `tunebook-files/` | Level 3 scholarly data — full per-song metadata — one JSON file per Level 3 edition. |
| `TUNEBOOK-CHANGELOG.md` | What has been added, and when. |
| `tunebook-page-extraction-guide.md` | What to look for when transcribing a tunebook page into structured data. Useful on its own, even outside this suite. |

## Using the data elsewhere

`tunebook-library.js` is a plain global-scope script, not a module — deliberately, because
`import`/`export` are blocked by browsers on `file://` URLs and every app in this suite must
work by double-clicking a file. It also exports itself under CommonJS, so Node can require
it directly:

```js
const library = require('./tunebook-library.js');
console.log(Object.keys(library.works).length, 'works');
```

In a browser it defines `EZ_MINUTES_TUNEBOOK_LIBRARY`.

## Levels

An Edition's level says how much is known about it, not how important it is:

- **Level 1** — a bare bibliographic record: the book exists, no page index yet. Most of the
  library, legitimately.
- **Level 2** — a complete page-and-title index, held in `tunebook-library.js`.
- **Level 3** — full per-song metadata in its own file under `tunebook-files/`.
