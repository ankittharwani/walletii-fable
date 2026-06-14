# Netlify Deployment Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Configure the walletii-fable static site for Netlify deployment with clean URLs (no `.html` extensions in the browser address bar) and a custom branded 404 page.

**Architecture:** Pure static HTML site — no build step. Netlify serves the repo root directly with `pretty_urls = true`, which makes `about.html` accessible at `/about`. All internal `href` attributes in the 5 HTML files are updated to match the clean URLs, eliminating any runtime redirects on navigation.

**Tech Stack:** HTML/CSS (static), Netlify

---

## File Map

| Action | Path | Responsibility |
|---|---|---|
| Create | `.gitignore` | Exclude `.venv/` and `.claude/` from git |
| Create | `netlify.toml` | Netlify build config, pretty URLs, security headers |
| Create | `404.html` | Branded "page not found" page with clean-URL nav |
| Modify | `index.html` | Update 9 internal `.html` hrefs to clean paths |
| Modify | `about.html` | Update 9 internal `.html` hrefs to clean paths |
| Modify | `features.html` | Update 9 internal `.html` hrefs to clean paths |
| Modify | `loyalty.html` | Update 10 internal `.html` hrefs to clean paths |
| Modify | `faq.html` | Update 9 internal `.html` hrefs to clean paths |

---

## Task 1: Initialise git

This project has no git repository yet. Must be done first so subsequent tasks can commit.

- [ ] **Step 1: Initialise the repo**

```bash
git init
git branch -M main
```

Expected output from `git init`: `Initialized empty Git repository in .../walletii-fable/.git/`

- [ ] **Step 2: Verify**

```bash
git status
```

Expected: `On branch main` and a list of untracked files (`index.html`, `about.html`, etc.). Confirm `.venv/` and `.claude/` are listed as untracked (they'll be excluded once `.gitignore` is in place).

---

## Task 2: Create `.gitignore`

**Files:**
- Create: `.gitignore`

- [ ] **Step 1: Create the file**

Create `.gitignore` at the repo root:

```
.venv/
.claude/
```

- [ ] **Step 2: Verify `.venv` and `.claude` are now ignored**

```bash
git status
```

Expected: `.venv/` and `.claude/` are no longer listed in untracked files.

- [ ] **Step 3: Commit**

```bash
git add .gitignore
git commit -m "chore: add .gitignore excluding .venv and .claude"
```

---

## Task 3: Create `netlify.toml`

**Files:**
- Create: `netlify.toml`

- [ ] **Step 1: Create the file**

Create `netlify.toml` at the repo root:

```toml
[build]
  publish = "."

[build.processing.html]
  pretty_urls = true

[[headers]]
  for = "/*"
  [headers.values]
    X-Frame-Options = "DENY"
    X-Content-Type-Options = "nosniff"
    Referrer-Policy = "strict-origin-when-cross-origin"
```

- [ ] **Step 2: Verify**

```bash
cat netlify.toml
```

Expected: exact content above, no typos in key names.

- [ ] **Step 3: Commit**

```bash
git add netlify.toml
git commit -m "feat: add netlify.toml with pretty URLs and security headers"
```

---

## Task 4: Update internal links in all HTML files

**Files:**
- Modify: `index.html`
- Modify: `about.html`
- Modify: `features.html`
- Modify: `loyalty.html`
- Modify: `faq.html`

The mapping for every `href` attribute across all five files:

| Before | After | Notes |
|---|---|---|
| `href="features.html#fees"` | `href="features#fees"` | Must run before `features.html` → `features` |
| `href="index.html#download"` | `href="/#download"` | Must run before `index.html` → `/` |
| `href="index.html"` | `href="/"` | Brand logo links on inner pages |
| `href="about.html"` | `href="about"` | |
| `href="features.html"` | `href="features"` | |
| `href="loyalty.html"` | `href="loyalty"` | |
| `href="faq.html"` | `href="faq"` | |

Note: `href="#top"`, `href="#rates"`, `href="#download"` on `index.html` are same-page anchors with no `.html` — they are untouched by these patterns.

- [ ] **Step 1: Run replacements on all five files**

Run from the repo root. Order matters — more-specific patterns first.

```bash
for f in index.html about.html features.html loyalty.html faq.html; do
  sed -i '' 's|href="features\.html#fees"|href="features#fees"|g' "$f"
  sed -i '' 's|href="index\.html#download"|href="/#download"|g' "$f"
  sed -i '' 's|href="index\.html"|href="/"|g' "$f"
  sed -i '' 's|href="about\.html"|href="about"|g' "$f"
  sed -i '' 's|href="features\.html"|href="features"|g' "$f"
  sed -i '' 's|href="loyalty\.html"|href="loyalty"|g' "$f"
  sed -i '' 's|href="faq\.html"|href="faq"|g' "$f"
done
```

- [ ] **Step 2: Verify — no internal `.html` hrefs remain**

```bash
grep -n 'href="[^#h][^t]*\.html"' index.html about.html features.html loyalty.html faq.html
```

Expected: **no output** (zero matches). If any lines print, re-run the sed command for that specific file.

- [ ] **Step 3: Verify — spot-check expected clean hrefs are present**

```bash
grep -c 'href="about"' index.html about.html features.html loyalty.html faq.html
```

Expected: each file prints `2`.

```bash
grep -c 'href="/"' about.html features.html loyalty.html faq.html
```

Expected: each file prints `1` (brand logo link).

- [ ] **Step 4: Commit**

```bash
git add index.html about.html features.html loyalty.html faq.html
git commit -m "feat: update internal links to clean URLs (no .html extensions)"
```

---

## Task 5: Create `404.html`

**Files:**
- Create: `404.html`

- [ ] **Step 1: Create the file**

Create `404.html` at the repo root:

```html
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Page not found — walletii</title>
<meta name="description" content="The page you're looking for doesn't exist.">
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Bricolage+Grotesque:opsz,wght@12..96,300..800&family=Figtree:wght@400;500;600;700&family=DM+Mono:wght@400;500&display=swap" rel="stylesheet">
<style>
:root{
  --ink:#07212e;--teal:#00c9b7;--teal-deep:#00897d;--red:#c8102e;--white:#fff;
  --line:rgba(7,33,46,.1);
  --disp:"Bricolage Grotesque",sans-serif;--body:"Figtree",sans-serif;
}
*{margin:0;padding:0;box-sizing:border-box}
html{scroll-behavior:smooth}
body{font-family:var(--body);color:var(--ink);background:#fff;line-height:1.55;-webkit-font-smoothing:antialiased;min-height:100vh;display:flex;flex-direction:column}
a{color:inherit}
.nav{position:fixed;top:14px;left:50%;transform:translateX(-50%);z-index:50;width:min(1140px,calc(100% - 28px));
  display:flex;align-items:center;justify-content:space-between;padding:10px 14px 10px 22px;border-radius:999px;
  background:rgba(255,255,255,.78);backdrop-filter:blur(14px);-webkit-backdrop-filter:blur(14px);
  border:1px solid var(--line);box-shadow:0 10px 32px -16px rgba(7,33,46,.25)}
.brand{display:flex;align-items:baseline;gap:8px;text-decoration:none}
.brand .w{font-family:var(--disp);font-weight:800;font-size:1.4rem;letter-spacing:-.03em}
.brand .w i{font-style:normal;color:var(--teal)}
.brand .by{font-size:.62rem;font-weight:600;letter-spacing:.08em;text-transform:uppercase;color:#6b7e89}
.brand .by b{color:var(--red)}
.nav-links{display:flex;gap:26px;list-style:none}
.nav-links a{text-decoration:none;font-weight:600;font-size:.92rem;color:#2c4250;position:relative}
.nav-links a::after{content:"";position:absolute;left:0;bottom:-5px;width:0;height:2px;background:var(--teal);border-radius:2px;transition:width .25s}
.nav-links a:hover::after{width:100%}
.btn{display:inline-flex;align-items:center;border:none;border-radius:999px;font-weight:700;font-size:.95rem;
  padding:13px 24px;text-decoration:none;transition:transform .2s,box-shadow .2s}
.btn:hover{transform:translateY(-2px)}
.btn-teal{background:var(--teal);color:var(--ink)}
.hero{flex:1;display:flex;align-items:center;justify-content:center;text-align:center;padding:140px 24px 80px}
.hero h1{font-family:var(--disp);font-size:clamp(5rem,14vw,9rem);font-weight:800;letter-spacing:-.03em;line-height:1;color:var(--teal);margin-bottom:4px}
.hero h2{font-family:var(--disp);font-size:clamp(1.4rem,3vw,2.2rem);font-weight:700;letter-spacing:-.02em;margin-bottom:14px}
.hero p{color:#3c5260;font-size:1.08rem;margin-bottom:32px}
@media(max-width:680px){.nav-links{display:none}}
</style>
</head>
<body>
<nav class="nav" aria-label="Main navigation">
  <a class="brand" href="/" aria-label="walletii home">
    <span class="w">wallet<i>ii</i></span>
    <span class="by">by <b>Ooredoo</b> Fintech</span>
  </a>
  <ul class="nav-links" role="list">
    <li><a href="about">About us</a></li>
    <li><a href="features">Features</a></li>
    <li><a href="loyalty">Loyalty</a></li>
    <li><a href="faq">FAQs</a></li>
  </ul>
  <a class="btn btn-teal" href="/#download">Download now</a>
</nav>
<main class="hero">
  <div>
    <h1>404</h1>
    <h2>Page not found</h2>
    <p>The page you're looking for doesn't exist or has moved.</p>
    <a class="btn btn-teal" href="/">Go to homepage</a>
  </div>
</main>
</body>
</html>
```

- [ ] **Step 2: Verify the file uses clean URLs internally**

```bash
grep 'href=' 404.html | grep -v 'https:\|tel:\|mailto:\|href="/\b'
```

Expected output: lines for `about`, `features`, `loyalty`, `faq` — none containing `.html`.

- [ ] **Step 3: Commit**

```bash
git add 404.html
git commit -m "feat: add branded 404 page"
```

---

## Task 6: Final check

- [ ] **Step 1: Confirm all files are committed**

```bash
git status
git log --oneline
```

Expected: clean working tree (`nothing to commit`). Log shows 4 commits on `main`:
```
feat: add branded 404 page
feat: update internal links to clean URLs (no .html extensions)
feat: add netlify.toml with pretty URLs and security headers
chore: add .gitignore excluding .venv and .claude
```

- [ ] **Step 2: Confirm docs are tracked**

```bash
git show --stat HEAD~3..HEAD | grep "docs/"
```

Expected: `docs/superpowers/specs/...` and `docs/superpowers/plans/...` should appear in the commit history.

If the `docs/` directory was not committed yet, stage and commit it:

```bash
git add docs/
git commit -m "docs: add design spec and implementation plan"
```

The site is now ready to push to GitHub and connect to Netlify.
