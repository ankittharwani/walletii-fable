# Netlify Deployment — Design Spec

**Date:** 2026-06-14
**Project:** walletii-fable

---

## Context

The project is a pure static HTML website: 5 `.html` files (`index.html`, `about.html`, `features.html`, `loyalty.html`, `faq.html`) with all CSS and JavaScript inline. External dependencies are limited to Google Fonts via CDN. There is no build step, no package manager, and no server-side logic.

The `.venv` Python virtual environment and `.claude` config directory are local tooling artefacts and must not be committed.

---

## Goal

Make the site deployable to Netlify with clean URLs (no `.html` extensions in the browser), zero navigation redirects, and good baseline security headers.

---

## Decisions

- **Clean URLs via `pretty_urls = true`** — Netlify serves `about.html` at `/about`. Combined with updating all internal links to drop `.html`, there are no runtime redirects on page navigation.
- **No build command** — publish directory is the repo root (`.`).
- **Custom 404** — a minimal branded page so broken links don't show Netlify's default.

---

## Files to Create

### `netlify.toml`

```toml
[build]
  publish = "."
  command = ""

[build.processing.html]
  pretty_urls = true

[[headers]]
  for = "/*"
  [headers.values]
    X-Frame-Options = "DENY"
    X-Content-Type-Options = "nosniff"
    Referrer-Policy = "strict-origin-when-cross-origin"

[dev]
  command = ""
  publish = "."
```

### `.gitignore`

```
.venv/
.claude/
```

### `404.html`

Minimal branded page matching the site's design system (same fonts, colours, nav) with a "Go home" button linking to `/`.

---

## Link Updates (all 5 HTML files)

Every internal `href` must be updated as follows:

| Before | After |
|---|---|
| `about.html` | `about` |
| `features.html` | `features` |
| `features.html#fees` | `features#fees` |
| `loyalty.html` | `loyalty` |
| `faq.html` | `faq` |
| `index.html` | `/` |
| `index.html#download` | `/#download` |

Same-page anchors (e.g. `#top`, `#rates`, `#download` on `index.html`) are unchanged.

---

## Out of Scope

- No serverless functions (no forms, no dynamic data)
- No environment variables
- No domain configuration (handled separately via Netlify dashboard)
- No build tooling or bundling introduced
