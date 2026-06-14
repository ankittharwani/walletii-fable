# walletii — fable

A reimagined marketing website for [walletii](https://www.walletii.com/om-en), the Ooredoo Fintech digital wallet for Oman.

## Origin

This site was generated with a single prompt to **Claude Fable** — Anthropic's experimental long-context model — instructing it to visit the live walletii website and produce an entirely new design and information architecture from scratch. No templates, no boilerplate; just the public site as reference material.

After the design was generated, the codebase was cleaned up and made deployable to Netlify using the **[Superpowers](https://github.com/anthropics/claude-code) skill for Claude Code** — which ran a full brainstorm → spec → implementation plan → execution workflow to add clean URLs, security headers, a custom 404 page, and git history.

## Pages

| Route | File |
|---|---|
| `/` | `index.html` — Hero, rates, features, download |
| `/about` | `about.html` — Mission and company background |
| `/features` | `features.html` — Full feature breakdown and fee guide |
| `/loyalty` | `loyalty.html` — walletii Points programme |
| `/faq` | `faq.html` — Frequently asked questions |

## Tech stack

Pure static HTML/CSS — no build step, no framework, no dependencies beyond Google Fonts (loaded via CDN). All styles and scripts are inline.

## Deploy to Netlify

### Option A — Netlify dashboard (recommended)

1. Push this repo to GitHub (already done).
2. Go to [app.netlify.com](https://app.netlify.com) → **Add new site → Import from Git**.
3. Select your GitHub repo.
4. Leave **Build command** empty and **Publish directory** as `.` — Netlify reads `netlify.toml` automatically.
5. Click **Deploy site**.

Clean URLs (`/about`, `/features`, etc.), security headers, and the custom 404 page are all configured in `netlify.toml` and activate on first deploy.

### Option B — Netlify CLI

```bash
npm install -g netlify-cli
netlify login
netlify init        # link to an existing site or create a new one
netlify deploy --prod
```

### Option C — Drag and drop

Zip the repo contents (excluding `.venv/` and `.claude/`) and drag the zip onto the Netlify dashboard at [app.netlify.com](https://app.netlify.com). Note: `netlify.toml` is respected for drag-and-drop deploys too.

## Local preview

No build step needed — open any `.html` file directly in a browser, or use any static file server:

```bash
# Python
python3 -m http.server 8000

# Node (npx)
npx serve .

# Netlify CLI (mirrors production behaviour including pretty URLs)
netlify dev
```

## Configuration

`netlify.toml` controls:

- **Publish directory** — repo root (`.`)
- **Pretty URLs** — `about.html` is served at `/about`, no `.html` in the browser
- **Security headers** — `X-Frame-Options`, `X-Content-Type-Options`, `Referrer-Policy` applied to all routes
- **404** — `404.html` is served automatically by Netlify for unmatched routes
