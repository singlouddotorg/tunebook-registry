# Tunebook Registry

A small, static, read-only website: browse every shape-note tunebook indexed by the
[EZ Minutes Suite](https://github.com/singlouddotorg/ezminutes) — full titles, editions, and
every page's song content — in a plain web page, no install required.

This is a **spinoff, not a fork.** It shares one file with the main suite
(`tunebook-index.js`) and nothing else: no app code, no build step, no dependencies. The two
projects can each move at their own pace. Growing EZ Minutes' schema (new per-song fields,
more books, richer edition-index data) never breaks this site — it just means more could
optionally be *shown* here later. This site never writes back to that data; browsing here
doesn't touch the working data in Capture, Compile, or Tunebook Editor.

## What's here

- **`index.html`** — the whole site. One file: markup, styling, and behavior together,
  matching the "no build step" philosophy of the rest of the EZ Minutes Suite. Visually
  styled to match the main suite (same palette, same badge pills, same shape-system glyph)
  so it reads as a sibling rather than a separate product.
- **`tunebook-index.js`** — a **copy** of the same file `capture.html`/`compile.html`/
  `tunebook-editor.html` load in the main suite. This is the actual data; `index.html` just
  displays it. See "Keeping this in sync," below.
- **`LICENSE`** — MIT, matching the main suite.

## Running it locally

Nothing to install. Open `index.html` directly in a browser — it works the same way every
other page in this suite does, by double-clicking the file. No server needed.

## Publishing it for free with GitHub Pages

GitHub Pages serves static files straight out of a repo at no cost — no server to pay for or
maintain, since this site has none. Steps, assuming this content lives in a new repo under
the `singlouddotorg` account (e.g. `singlouddotorg/tunebook-registry`, as a sibling to
`singlouddotorg/ezminutes`):

1. **Create the repo.** On GitHub, under the `singlouddotorg` account: New repository →
   name it (e.g. `tunebook-registry`) → Public → don't initialize with a README (this folder
   already has one) → Create.
2. **Push this folder's contents to it** — either via GitHub's own "upload files" web UI
   (drag in `index.html`, `tunebook-index.js`, `LICENSE`, `README.md`) or via `git`:
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

The only manual step going forward: whenever `tunebook-index.js` gets a new release in the
main EZ Minutes Suite repo (a new book, corrected pages, updated book-level fields), copy
that same file into this repo and push.

```
cp /path/to/ezminutes/tunebook-index.js ./tunebook-index.js
git add tunebook-index.js
git commit -m "Sync tunebook-index.js to vX.X.X"
git push
```

`index.html` itself doesn't need to change for this — it reads whatever `EZ_MINUTES_TUNEBOOKS`
and `EZ_MINUTES_TUNEBOOK_INDEX_VERSION` happen to be in the loaded file, and shows the current
version number in the site's footer as a quick sanity check that the copy is current.

## Feedback

This is a pre-release, field-test-feedback-welcome companion to a pre-release suite. Every
book page links a "Report an error" / "Send feedback" address
(`sacredharpbiblio@gmail.com`) in the footer and header. If this repo grows GitHub Issues
enabled, that's a fine place for corrections too — either way works.

## What this deliberately doesn't do (yet)

- No editing. This is Browse, not Tunebook Editor — nothing here writes anywhere. Corrections
  go back through the main suite's own process, not through this site.
- No richer per-song data (meter, key, attributions, first lines) yet, even though
  `tunebook-index.js`'s schema and some books' `edition-indexes/*.json` files already support
  it. Only page + title is used here for now, matching what every book is guaranteed to have.
  Showing more is a possible future enhancement, not a requirement — see the main suite's
  `CONSIDERED-OPTIONS.md` if this gets picked up as an actual backlog item there.
