# Copilot instructions for 2GT Jekyll site

Summary
- This repository is a Jekyll-based blog built from the `jekyll-theme-yat` theme. It produces a static `_site/` output which can be served directly (see `Dockerfile`).

Quick commands
- Install deps: `bundle install`
- Local preview: `bundle exec jekyll serve` (or `JEKYLL_ENV=development bundle exec jekyll serve`)
- Production build: `JEKYLL_ENV=production bundle exec jekyll build` — output appears in `_site/`
- Docker (uses `_site/`): `docker build -t 2gt-site .` then `docker run -p 80:80 2gt-site`

Big-picture architecture
- Content: `_posts/` contains articles named `YYYY-MM-DD-title.md` with YAML front matter.
- Templates: `_layouts/` (page shells) and `_includes/views/` (reusable view fragments). Example: `_layouts/default.html` includes `views/header.html` and `views/footer.html`.
- Logic helpers: `_includes/functions/*.html` contain reusable Liquid helpers (e.g., `get_reading_time.html`, `get_article_excerpt.html`). Prefer adding or updating those for cross-site logic.
- Assets: SCSS lives under `_sass/` and compiled by Jekyll into `assets/css/`; images in `assets/images/` and videos in `assets/videos/`.
- Theme packaging: `jekyll-theme-yat.gemspec` declares runtime dependencies (jekyll and common plugins). Editing theme files directly in this repo is expected.

Project-specific conventions
- Front matter: site-level settings in `_config.yml` (e.g., `header_pages`, `theme_color`, `yat.date_format`). Posts should respect banner/front-matter keys used by templates.
- Extensions: optional features live under `_includes/extensions/` (e.g., `theme-toggle.html`, `mathjax.html`). Toggle behavior by editing _includes or `_config.yml` flags (e.g., `google_analytics`, `disqus`, `utterances`).
- Date format: site uses `yat.date_format` (`%b %d, %Y`) — use this when generating or validating display dates.

Where to make changes
- Content changes: add or edit files in `_posts/` and top-level pages (e.g., `about.html`, `articles.html`).
- Structural/template changes: edit `_layouts/` and `_includes/views/`.
- Shared logic/helpers: update `_includes/functions/*.html` — changes here affect all views that include them.
- Styles: modify `_sass/yat/` or `_sass/misc/` for theme-level styling.

Integration points & external dependencies
- Plugins: declared in `_config.yml` and `jekyll-theme-yat.gemspec` (jekyll-feed, jekyll-seo-tag, jekyll-sitemap, jekyll-paginate, jekyll-spaceship).
- Analytics/comments: configure `google_analytics`, `disqus`, `gitment`, or `utterances` in `_config.yml`.
- Deployment: CI/GitHub Actions implied by README badge; production container uses `Dockerfile` which expects built `_site/`.

Examples to reference when coding
- Edit header/footer: `_includes/views/header.html` and `_includes/views/footer.html`
- Add a helper: `_includes/functions/get_reading_time.html`
- Example post: `_posts/2025-02-12-storage-server.md` (follow its front-matter and content structure)

Rules for AI edits
- Prefer updating `_includes/functions/` for cross-cutting logic rather than duplicating logic in multiple templates.
- Do not modify `_site/` — it's a build artifact. Changes should be made in source files and rebuilt.
- When adding or changing a plugin, ensure it's reflected in `jekyll-theme-yat.gemspec` and `_config.yml`.

If anything is unclear or you'd like examples expanded, tell me which area to expand (build, templates, helpers, or deployment).
