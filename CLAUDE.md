# ScoringSpades

A scoring app for the card game Spades. Single-page web app at scoringspades.com.

## Tech

- `index.html` (app) + `how-to-play.html` (rules page) + `manifest.json` + `icon.svg`. No build step, no dependencies, no framework.
- Vanilla JS with a tiny custom `el(tag, attrs, ...children)` render helper (not React).
- Inline CSS, inline SVG favicon, inline JS — everything ships in one file (~48 KB).
- State persisted to `localStorage` under key `spades-state-v1`.
- Mobile-first (iOS PWA meta tags, safe-area insets) — viewport is locked, no user-scalable.
- Google Analytics: `G-0J06PZ892T` (loaded from gtag).

## App structure (inside `index.html`)

- **State/phases:** `setup` → `playing` → `gameover`. `modal` is a separate global: `bid`, `tricks`, or `preview`.
- **Render entry:** `render()` routes on `state.phase`. Each screen has a `renderX()` function that calls `app.replaceChildren(...)`.
- **Scoring model** (`scoreTeamRound`): `bid × 10` if they hit, `−bid × 10` if they miss, `+1` per bag. Bag overflow: every 10 bags = `−100` penalty. Nil = ±100, Blind nil = ±200.
- **Legacy name migration:** old default placeholder names (`Player 1`–`Player 4`) are wiped on load so the user isn't stuck with them.

## Deploy (Cloudflare Worker + Static Assets)

**This is a Worker, not a Pages project** — the original Pages project (`spades` / `spades-7wr.pages.dev`) was migrated. Don't try `wrangler pages deploy`; it will fail with "Project not found."

- **Worker name:** `scoringspades` (not `spades`)
- **Account:** `851a39c5483b9aef842112771b5f8542` (patrick@patrickturner.net)
- **Domains:** `scoringspades.com`, `www.scoringspades.com`, `scoringspades.patrick-851.workers.dev`
- **Mechanism:** Workers Static Assets (`assets.directory` binding). No custom Worker logic — static files served directly. `_headers` and `_redirects` are supported (Pages-compatible behavior).

### Deploy

`wrangler.jsonc` lives at the repo root (added by Cloudflare's GitHub auto-config bot in PR #1, April 2026), so deploy is one command from the project root:

```
cd ~/Documents/Dev/ScoringSpades
wrangler deploy

# Verify
curl -sI https://scoringspades.com/ | grep -iE "^(content-security|strict-transport|x-content|referrer-policy|permissions-policy)"
```

Auth: already logged in as patrick@patrickturner.net via `wrangler` OAuth. Verify with `wrangler whoami`.

### Heads-up: `assets.directory` is `"."` (whole repo)

The committed `wrangler.jsonc` ships **the entire repo root** as static assets. Anything visible at the repo root is potentially servable at `https://scoringspades.com/<filename>`. Currently:

- `LICENSE`, `README.md`, `CLAUDE.md`, `wrangler.jsonc` would all be servable if Cloudflare didn't filter them. In practice they 404 today — wrangler's asset upload appears to skip dotfiles and certain config files — but **don't rely on this**. If you ever drop a file at the repo root that contains anything sensitive, it could be served.
- Safer fix when you're next in here: move deployable files into a `public/` subdir and change `assets.directory` to `"./public"`. Or add `assets.exclude` if wrangler supports it (check current docs).
- Don't put `.env`, secrets, or local-only notes at the repo root.

### Notes & gotchas

- `_headers` is a **config file**, not a static asset. `wrangler deploy` will report it in the file count but it's interpreted by Cloudflare to set response headers, not served at `/_headers` (that path returns 404).
- The local `.wrangler/cache/wrangler-account.json` only contains the public `account.id` and `account.name` — no token. (The OAuth token lives at `~/.wrangler/config/default.toml`.) The old "deploy from clean staging dir to avoid leaking the wrangler cache" rule was based on a misunderstanding.
- If `wrangler.jsonc` ever disappears, recover it with: `cd /tmp && wrangler init --from-dash scoringspades --yes` (creates a subdir with the dashboard's current config). **Never run that command inside the `ScoringSpades/` repo dir** — macOS case-insensitivity creates a confusing nested `scoringspades/` directory.

### CSP / security headers

Production headers come from `_headers`. CSP allowlists `googletagmanager.com` + `google-analytics.com` for gtag. If you add any new third-party script (Stripe, Cloudflare Turnstile, Sentry, etc.), update `script-src` and likely `connect-src` in `_headers` or the page will silently break with CSP violations in the browser console.

## GitHub

Repo: `turnepf/ScoringSpades` on GitHub. `gh` CLI is authenticated as `turnepf`. Push with `git push` — no CI/CD hook to Cloudflare yet, so deploys are still manual.

## Monetization

Venmo tip jar (`@turnepf`) on setup screen (small pill) and winner screen (card). Links go to `https://venmo.com/u/turnepf`. No fees, no third-party account. Venmo has no web-checkout — link opens app on mobile, shows profile + QR on desktop.
