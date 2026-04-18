# ScoringSpades

A scoring app for the card game Spades. Single-page web app at scoringspades.com.

## Tech

- Single `index.html` file. No build step, no dependencies, no framework.
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

## Deploy (Cloudflare Pages)

Project name is **`spades`** (not `scoringspades` — the CF project kept its original name). Direct upload, no Git integration yet. Domains: `scoringspades.com`, `www.scoringspades.com`, `spades-7wr.pages.dev`.

Deploy from a **clean staging directory** — never from the project root, because `.wrangler/cache/wrangler-account.json` contains an auth token that must not be uploaded.

```
rm -rf /tmp/spades-deploy && mkdir /tmp/spades-deploy
cp index.html /tmp/spades-deploy/
wrangler pages deploy /tmp/spades-deploy --project-name=spades --commit-message="<msg>"
```

Auth: already logged in as patrick@patrickturner.net via `wrangler` OAuth. Verify with `wrangler whoami`.

## GitHub

Repo: `turnepf/ScoringSpades` on GitHub. `gh` CLI is authenticated as `turnepf`. Push with `git push` — currently no CI/CD hook to Cloudflare Pages, so deploys are still manual via the wrangler command above.

## Monetization

Venmo tip jar (`@turnepf`) on setup screen (small pill) and winner screen (card). Links go to `https://venmo.com/u/turnepf`. No fees, no third-party account. Venmo has no web-checkout — link opens app on mobile, shows profile + QR on desktop.
