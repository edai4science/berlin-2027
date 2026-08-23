# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

The website for the "AI Driven Discovery Systems in Science" workshop (Berlin, 23-24 Feb 2027), built on the
[al-folio](https://github.com/alshedivat/al-folio) Jekyll theme (this fork was seeded from a prior workshop site,
spigmworkshop2024.github.io). It is a static content site — organizer/speaker info, call for abstracts, schedule,
location — not an application with business logic.

## Commands

Run locally with Docker (no local Ruby/Jekyll install needed):

```bash
docker build -t aidiscovery4science-site .
./bin/docker_run.sh          # serves at http://localhost:4000, live-reloads on file changes
```

Or with a local Ruby/bundler toolchain:

```bash
bundle install
bundle exec jekyll serve     # local dev server with live reload
bundle exec jekyll build     # production build -> ./_site (also: bin/cibuild)
```

There is no test suite or linter in this repo. Verify changes by building the site and checking the rendered
`_site` output / dev server rather than by looking for CI checks.

## Deployment

Deployment is fully automatic: pushing to `main` triggers `.github/workflows/jekyll-gh-pages.yml`, which builds
the Jekyll site and publishes it to GitHub Pages. There is no `gh-pages` branch step to do by hand — `bin/deploy`
is a legacy al-folio script for the older manual (non-Actions) deploy flow and is not part of the current pipeline.

## Architecture

- **`_pages/*.md`** — top-level site pages (About, Call for Abstracts, Schedule, Location, publications/projects
  dropdown). Each has YAML front matter controlling `layout`, `permalink`, and `nav_order` (nav bar ordering is
  driven by sorting all pages by `nav_order`, see `_includes/header.html`).
- **`_layouts/workshop.html`** — the layout used by the homepage (`_pages/about.md`, `layout: workshop`). This is
  where the workshop abstract, "Confirmed Speakers" grid, "Organizers" grid, and sponsor logos live. **Speaker and
  organizer entries are hand-authored HTML card blocks directly in this file** — they are not data-driven from
  `_data/*.yml` (those YAML files exist from the theme scaffold but are effectively unused/empty). To add/edit a
  speaker or organizer, copy an existing `.card`/`.card-item` block in this file and edit it in place.
- **`_layouts/`** (other files) — generic al-folio layouts (`default`, `page`, `post`, `cv`, `bib`, `distill`,
  archive pages) inherited from the theme; mostly untouched plumbing for this workshop site.
- **`_includes/header.html`** — nav bar; page order comes from `nav_order` front matter on each page.
- **`_config.yml`** — global site settings (title, contact, social links, feature toggles like
  `enable_darkmode`, `enable_masonry`, `imagemagick`, `scholar`, `jekyll-paginate-v2`, etc.), inherited from
  al-folio. Most feature flags are irrelevant to this workshop site (blog/CV/publications features are theme
  defaults, not actively used) — check whether a page under `_pages/` actually references a feature before
  assuming it's live.
- **`assets/img/`** — original speaker/organizer photos and logos, named to match the person (e.g. `yashas.jpg`).
- **`assets/resized/`** — pre-generated responsive image variants (`<name>_480.*`, `_800.*`, `_1400.*`) referenced
  via `srcset` in the workshop layout's card markup. `imagemagick.enabled` is currently `false` in `_config.yml`,
  so these are **not** regenerated automatically on build — they were produced once and committed. If you add a
  new speaker photo, generate matching `_480/_800/_1400` variants yourself (or via ImageMagick) and drop them in
  `assets/resized/`, following the existing naming convention, rather than relying on the build to do it.
- **`_plugins/`** — two small custom Ruby plugins (`external-posts.rb` for pulling in externally-hosted blog
  posts, `hideCustomBibtex.rb` for the publications/bib layout); both are theme leftovers not exercised by any
  current page on this site.

## Content editing notes

- Placeholder/TBD content (e.g. `_pages/schedule.md`, `description: TBD` in `_pages/call.md`) reflects that the
  workshop program isn't finalized yet — don't treat "TBD" as a bug to silently fill in with invented content.
- Contact email throughout the site is `edai4science@gmail.com`; keep it consistent if it changes.
