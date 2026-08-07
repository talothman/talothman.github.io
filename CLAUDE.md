# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

This is a Jekyll static site (personal blog/portfolio for Talal Alothman, VR/AR/XR developer & researcher), hosted on GitHub Pages at `talothman.github.io`. There is no JS build system, package manager, or test suite — content is Markdown/HTML with a Sass stylesheet, compiled by Jekyll.

## Commands

```bash
bundle install              # install gems (uses the `github-pages` gem to match GH Pages' Jekyll version)
bundle exec jekyll serve    # local dev server with live reload, http://localhost:4000
bundle exec jekyll build    # production build into _site/ (gitignored)
```

Ruby version is pinned via `.ruby-version` (3.3.4, matching GitHub Pages — see comment in `Gemfile`). There is no lint or test command in this repo.

Deployment is automatic: GitHub Pages builds and serves directly from the `master` branch on push — there is no CI/deploy step to trigger manually.

### Dependencies are capped by GitHub Pages

`bundle outdated` reports ~40 stale gems. **Almost none of them are actionable.** GitHub Pages builds this site server-side with its own gem set, and the `github-pages` gem exists to mirror it locally — it pins Jekyll, kramdown, and every plugin to exact versions:

| | version | can it move? |
|---|---|---|
| `github-pages` | 232 | no — 232 is both the newest release and what GH Pages runs |
| `jekyll` | 3.10.0 | no — pinned `= 3.10.0`; Jekyll 4.x is not supported by the GH Pages builder |
| `jekyll-seo-tag` | 2.8.0 | no — pinned `= 2.8.0` |
| `jekyll-sitemap` | 1.4.0 | no — pinned `= 1.4.0` |
| `kramdown` | 2.4.0 | no — pinned `= 2.4.0` |
| `nokogiri`, `webrick`, `mercenary`, `terminal-table` | — | yes, loosely pinned |

So `bundle update` only moves transitive gems. Do not add version constraints for plugins in the `Gemfile` — `github-pages` is the single source of truth, and a duplicate pin makes the bundle unresolvable when it bumps. Upgrading Jekyll itself requires abandoning the built-in builder for a GitHub Actions workflow; that's a deliberate migration, not a dependency bump.

Local gem versions have no bearing on the published site (GitHub rebuilds with its own), so `Gemfile.lock` is gitignored on purpose. After any `bundle update`, confirm nothing rendered differently:

```bash
cp -r _site /tmp/site-before && bundle exec jekyll build && diff -r /tmp/site-before _site
```

## Architecture

Standard Jekyll collections/layouts structure:

- `_config.yml` — site config: SEO metadata (`site.author`, `site.social`, `site.description`), the `projects` collection definition, plugins (`jekyll-seo-tag`, `jekyll-sitemap`), and the `defaults` block that assigns layouts/images per content type.
- `_layouts/` — `default.html` (base wrapper: head/meta/SEO/analytics + `{{ content }}`) is used by `post`, `projects`, and `about`. `default_no_seo.html` is a copy of `default.html` without `{% seo %}` / structured data, used for one-off standalone pages (e.g. `pages/saadeh_family_rep_campaign.md`) that shouldn't carry the site's Person/Website schema.
- `_includes/` — `analytics.html` (Google gtag, injected only when `jekyll.environment == 'production'`) and `structured-data.html` (JSON-LD: Person + WebSite schema always, plus BlogPosting schema conditionally when `page.layout == 'post'`).
- `_posts/` — blog posts, standard `YYYY-MM-DD-Title.md` filenames, `layout: post` front matter. Permalink pattern is `/blog/:title` (set in `_config.yml`, note the discrepancy with the `_posts` dir name vs the `/blog` URL prefix).
- `_projects/` — a custom Jekyll collection (`output: true` in `_config.yml`), rendered via `_layouts/projects.html` which loops `site.projects` sorted by `date` descending. Individual project pages have no visible per-page layout of their own — the collection's `defaults` in `_config.yml` set `layout: default`, and `projects/index.html` (layout: `projects`) renders each project's `{{ project.content }}` inline in a list — so project markdown files are just content fragments, not standalone pages.
- `_data/navigation.yml` — drives the nav menu on `index.html`. `_data/websites.yml` exists but is currently empty.
- `pages/` — one-off standalone pages outside the normal content model, using `default_no_seo` layout and a custom `permalink` (e.g. `pages/saadeh_family_rep_campaign.md` → `/saadeh/`). Prefer this pattern for future ad-hoc pages that aren't posts or projects.
- `_sass/` + `assets/css/` — `_config.scss` holds color/breakpoint variables; `native.scss` is the main stylesheet entry (compiled with `sass.style: :compressed`).
- `me/index.md` — the About page; uses `_layouts/about.html`, which expects `page.sections[0]` (English) and `page.sections[1]` (Arabic) as Textile-rendered content blocks (note: uses `textilize`, not Markdown, for this one layout).

## SEO invariants

This site exists largely to rank for the owner's name, so a few things are load-bearing:

- **Never add `<title>` or `<meta name="description">` to a layout.** `{% seo %}` in `_layouts/default.html` emits both. A second copy makes Google discard the tag and guess the page name from the visible `<h1>` instead — which is how the homepage ended up indexed as "Dispatches/أيفادات" rather than the owner's name.
- **Liquid executes inside HTML comments.** `<!-- ... {% seo %} ... -->` renders the whole SEO block again. Use `{% comment %}` when referring to Liquid tags in prose.
- **Keep "Talal Alothman" in visible body text** (homepage byline, post bylines, about `<h1>`), not just in meta tags and JSON-LD. Google weights rendered text far more heavily for name queries; meta-only mentions were being ignored.
- `site.title` is the plain personal name because jekyll-seo-tag uses it for `og:site_name` and appends it to every page title (`Post Title | Talal Alothman`). `site.tagline` holds the descriptor. `site.eng-name`/`ara-name` remain the *visual* brand ("Dispatches / إيفادات") — those are separate on purpose.
- JSON-LD in `_includes/structured-data.html` is one linked graph: all nodes reference the Person via `@id: {{ site.url }}/#person`. Don't inline duplicate Person objects, and don't declare a `SearchAction` — the site has no search endpoint, and pointing at a nonexistent `/search?q=` is invalid structured data.
- `_layouts/default_no_seo.html` intentionally has a manual `<title>` and no `{% seo %}`/structured data. That is correct for it — don't "fix" it to match `default.html`.

After changing any of the above, rebuild and verify each page has exactly one title and one description:

```bash
for f in $(find _site -name '*.html'); do echo "$(grep -c '<title>' $f) $(grep -c 'name=\"description\"' $f) $f"; done
```

## Content conventions

- Post front matter: `layout: post`, `title`, `author`, `date` (with `-0400` offset), `description`, `image`, `tags`.
- Project front matter: `title`, `description`, `date`, `tags` — no `layout` needed (comes from collection defaults).
- `site.name` / `site.eng-name` / `site.ara-name` in `_config.yml` are custom template variables (not part of Jekyll's standard `site` object) used for the bilingual (English/Arabic) site title.
