# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

`tekstogtal` is the personal website of Lasse Hjorth Madsen (data scientist and freelance journalist), published at **tekstogtal.dk**. It is a Quarto website built from a handful of top-level `.qmd` files. **Write new prose in Danish** — that is the site's language. The one deliberate exception is `avatar.qmd`, which is entirely in English: it exists both to offer the chat and to serve visitors who don't read Danish, so its navbar entry is labelled `Chat with me` rather than a Danish title. Don't put a flag emoji there — a 🇬🇧 was tried and renders as the letters `GB` on Windows, which has no country-flag glyphs in its emoji font.

There is no build step beyond Quarto and no executable code: none of the `.qmd` files contain R (or any other) code chunks, so rendering is pure Markdown→HTML. The `DESCRIPTION` file and `tekstogtal.Rproj` are vestigial R-package scaffolding from how the project was originally created — they do not describe a real package and nothing depends on them.

## Commands

Run from the repo root (requires the `quarto` CLI on PATH; it is not available in the default bash shell here):

```bash
quarto preview                  # live-reloading local preview
quarto render                   # render the whole site into docs/
quarto render nonfiktion.qmd    # render a single page
quarto publish gh-pages         # render + push the output to the gh-pages branch
```

There are no tests or linters.

## Publishing and the docs/ directory

- `_quarto.yml` sets `output-dir: docs`, but `docs/` is **gitignored** (a leftover from the R `.gitignore` template). The rendered site is therefore never committed to `main`.
- Deployment happens via the `gh-pages` branch, whose root is the rendered site. `quarto publish gh-pages` **replaces** the branch contents with the output dir — anything on `gh-pages` that isn't in `docs/` gets deleted.
- That is why the custom domain lives in a tracked root `CNAME` (`tekstogtal.dk`, single line) listed under `resources:` in `_quarto.yml`, so each render copies it into `docs/` and each publish carries it through. **Don't remove either half.** History: `7385ebd` set this up correctly in May 2023 but with two domains in the file, which GitHub Pages rejects (it allows one); the fix in `c013425` was then over-corrected by `0998f1f`/`916202d`, which deleted the mechanism entirely. Two months later publish `b4bf97a` duly deleted CNAME from `gh-pages` and took the domain down until `20a59ee` restored it. `historiebutikken.dk` was dropped and is not coming back — a second domain would need a DNS-level redirect, not a second line here.
- `_site/` is stale output from before `output-dir` was switched to `docs` (it still contains a long-deleted `about.html`). It was tracked in git until it was untracked with `git rm -r --cached`; the files remain on disk and `.gitignore` covers them. It is not part of the live site.

## Structure and conventions

- Each navbar page is a top-level `.qmd` registered under `website.navbar.left` in `_quarto.yml`. Adding a page means creating the `.qmd` *and* adding the `href`/`text` pair there.
- `notes.qmd` is intentionally not in the navbar — it is the author's private scratchpad of Quarto workflow notes and a to-do list, rendered but unlinked.
- Content pages are lists of works (articles, books, talks, projects) written as Markdown bullets with a bolded title link, a one-line description, and an italicised publication credit. Cover art and article thumbnails go in the margin:

  ```markdown
  ::: column-margin
  ![](images/aktier.png)
  :::
  ```

- Static assets are checked in and referenced by relative path: `images/` (covers, thumbnails), `pdf/` (scanned newspaper articles), `presentations/` (self-contained rendered slide decks linked from `foredrag.qmd`), `html/` (a standalone imported page).
- `styles.css` is effectively empty; theming comes from the `litera` Bootswatch theme. Per-page CSS is inlined in the `.qmd` instead (see `avatar.qmd`).

## avatar.qmd

"Chat with me" embeds a separately deployed chat app from `https://avatar.tekstogtal.dk` in a full-bleed iframe. The inline script reads a `?q=N` query parameter off the site URL and forwards it into the iframe `src`, so `tekstogtal.dk/avatar.html?q=2` opens the page with question 2 pre-answered. That app lives in another repository — changes to its behaviour are not made here.
