# Tunebook Registry

A small, static, read-only website: browse every shape-note tunebook indexed by the
[Minutes / Tunebooks suite](https://github.com/singlouddotorg/ezminutes) — full titles, editions,
every page's song content, and scholarly detail (meter, key, scripture reference,
attributions, copyright status, publication history, sources) for any book that has it — in
a plain web page, no install required.

This is a **spinoff, not a fork.** It shares three things with the main suite
(`tunebook-library.js`, `shared-utils.js`, and the `tunebook-files/` directory) and nothing
else: no app code, no build step, no dependencies. The two projects can each move at their
own pace. Growing the main suite's schema (new per-song fields, more books, richer Edition
data) never breaks this site — it just means more could optionally be *shown* here later.
This site never writes back to that data; browsing here doesn't touch the working data in
Minutes or Tunebooks.

## What's here

- **`index.html`** — the whole site. One file: markup, styling, and behavior together,
  matching the "no build step" philosophy of the rest of the Minutes / Tunebooks suite.
  Visually styled to match the main suite (same palette, same badge pills, same shape-system
  glyph) so it reads as a sibling rather than a separate product.
- **`codes.html`** — a static FAQ page: what Work/Edition codes, the shape-note glyphs, and
  the Level badges mean, plus a brief sketch of the Minutes and Tunebooks apps this data
  comes from. Deliberately has no data dependency of its own (no `<script src>` tags) and
  is kept to broad, stable concepts rather than specifics likely to drift - update it only
  when one of those underlying concepts itself genuinely changes, not for routine data
  syncs.
- **`tunebook-library.js`** — a **copy** of the main suite's Tunebook Library: every Work
  and Edition it knows about, including the full page-by-page song index for each indexed
  Edition. This is the actual base data; `index.html` derives its own book list from it (via
  `EZMinutesShared.buildTunebookIndexFromLibrary()`, the same projection Minutes and
  Tunebooks both use). See "Keeping this in sync," below.
- **`shared-utils.js`** — a **copy** of the main suite's shared helper file (page sorting,
  HTML escaping, the Library-to-legacy-shape projection). `index.html` won't render its book
  list without it, so it must be deployed alongside. Kept in sync the same way
  `tunebook-library.js` is, below.
- **`tunebook-files/`** — a **copy** of the main suite's Level 3 Tunebook Files: real
  scholarly and per-song enrichment for any Edition that has it. `index.html` fetches these
  lazily, one at a time, only for a book whose detail view is actually opened - not all at
  once on load. A book without a matching file in here still shows everything the Library
  itself carries (title, page, and every Library-level field); it just won't have the richer
  detail. This directory is optional in the sense that the site still works without it, but
  any book's own richer detail depends on its file actually being present here.
- **`LICENSE`** — MIT, matching the main suite.

## Running it locally

Nothing to install. Open `index.html` directly in a browser — it works the same way every
other page in this suite does, by double-clicking the file. No server needed for the basic
Library data. **One real limitation locally:** the Level 3 scholarly-detail fetch uses
`fetch()`, which most browsers block for local `file://` pages (the same restriction the
main suite works around elsewhere with `FileReader` instead, which isn't practical here
since this page doesn't know in advance which book a visitor will open). Locally, a book's
Level 3 detail may quietly not load; this isn't an error, and every other part of the page
keeps working. Once genuinely served over HTTP (GitHub Pages, or any local dev server), the
fetch works normally.


## Publishing it for free with GitHub Pages

GitHub Pages serves static files straight out of a repo at no cost — no server to pay for or
maintain, since this site has none. Steps, assuming this content lives in a new repo under
the `singlouddotorg` account (e.g. `singlouddotorg/tunebook-registry`, as a sibling to
`singlouddotorg/ezminutes`):

1. **Create the repo.** On GitHub, under the `singlouddotorg` account: New repository →
   name it (e.g. `tunebook-registry`) → Public → don't initialize with a README (this folder
   already has one) → Create.
2. **Push this folder's contents to it** — either via GitHub's own "upload files" web UI
   (drag in `index.html`, `tunebook-library.js`, `shared-utils.js`, `LICENSE`, `README.md`) or via `git`:
   ```
   git init
   git add .
   git commit -m "Initial Tunebook Registry"
   git branch -M main
   git remote add origin https://github.com/singlouddotorg/tunebook-registry.git
   git push -u origin main
   ```
3. **Turn on Pages.** In the repo: Settings → Pages → under "Build and deployment," set
   Source to "Deploy from a branch," Branch to `main`, folder to `/ (root)` → Save.
4. **Wait a minute, then visit it.** GitHub will publish it at
   `https://singlouddotorg.github.io/tunebook-registry/`. First deploy can take a minute or
   two; after that, every push to `main` republishes automatically.
5. **Optional: a custom domain.** If SingLoud.org wants this at its own subdomain (e.g.
   `tunebooks.singloud.org`), add a `CNAME` file to the repo root containing just that
   hostname, then add a matching `CNAME` DNS record at the domain registrar pointing to
   `singlouddotorg.github.io`. GitHub's Pages settings page will confirm once it's detected
   and valid.

Everything above is a one-time setup. After that, publishing an update is just "push to
`main`" — no redeploy step, no build to trigger.

## Keeping this in sync

The only manual step going forward: whenever `tunebook-library.js` (or `tunebook-files/`)
gets a new release in the main Minutes / Tunebooks suite repo (a new book, corrected pages,
a new or updated Level 3 Tunebook File, updated Edition fields), copy the same files into
this repo and push.

```
cp /path/to/ezminutes/tunebook-library.js ./tunebook-library.js
cp /path/to/ezminutes/shared-utils.js ./shared-utils.js
rm -rf ./tunebook-files
cp -r /path/to/ezminutes/tunebook-files ./tunebook-files
git add tunebook-library.js shared-utils.js tunebook-files
git commit -m "Sync tunebook-library.js and tunebook-files/ to vX.X.X"
git push
```

`index.html` itself doesn't need to change for this — it derives its book list from whatever
`EZ_MINUTES_TUNEBOOK_LIBRARY` happens to be in the loaded file, and fetches whatever's
actually present in `tunebook-files/` for a given book on demand. The site's own footer
shows the loaded file's real schema version as a quick sanity check that the copy is
current.

## Feedback

This is a pre-release, field-test-feedback-welcome companion to a pre-release suite. Every
book page links a "Report an error" / "Send feedback" address
(`sacredharpbiblio@gmail.com`) in the footer and header. If this repo grows GitHub Issues
enabled, that's a fine place for corrections too — either way works.

## What this deliberately doesn't do (yet)

- No editing. This is Browse, not Tunebooks (the main suite's own editor) — nothing here
  writes anywhere. Corrections go back through the main suite's own process, not through
  this site.
- Richer per-song data (meter, key, scripture reference, attributions, copyright status,
  first lines) and book-level scholarly detail (editorial history, publication history,
  sources, bibliography, relationships) now show for any book with a real Level 3 Tunebook
  File - filled in unevenly across the catalog by design, since that reflects the real,
  current state of the underlying research, not a limitation of this page. A book without a
  Tunebook File yet still shows everything the Library itself carries.

