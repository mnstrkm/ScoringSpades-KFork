# ScoringSpades

A clean, mobile-first scorekeeper for the card game Spades. Live at **[scoringspades.com](https://scoringspades.com)**.

## What it does

- 2 teams of 2, bid-by-team, standard Spades scoring
- Nil and blind nil support per-player
- Automatic bag tracking with the −100 penalty at every 10 bags
- Round-by-round history with per-round deltas
- Persists your game in `localStorage` — close the tab, come back, pick up where you left off
- Configurable target score (250 / 300 / 500 / custom)
- Installable to iOS home screen (PWA-style meta tags, safe-area handled)

## Tech

A single `index.html` file (~50 KB). No build step, no dependencies, no framework. Vanilla JS with a tiny custom render helper. A companion `how-to-play.html` page covers the rules. Hosted as a Cloudflare Worker with the Static Assets binding; `_headers` sets the security headers (CSP, HSTS, etc.).

## Run locally

Just open `index.html` in any modern browser. There's no server required for development. If you want a real local URL, the easiest option is:

```sh
npx serve .
```

…which serves the directory at `http://localhost:3000`.

## Self-host / fork it

The MIT license lets you fork and deploy your own copy. Because everything is static, **any static file host works** — Cloudflare Pages, Cloudflare Workers, Netlify, Vercel, GitHub Pages, S3+CloudFront, your own Apache/nginx, etc.

### 1. Things you'll want to change before deploying

These are hardcoded to me (Patrick) and you should swap them out:

| What | Where | Replace with |
|---|---|---|
| Google Analytics ID `G-0J06PZ892T` | `index.html` (2 lines near the top), `how-to-play.html` (same 2 lines) | Your own GA4 measurement ID, or delete the two `<script>` tags entirely to drop GA |
| Venmo handle `turnepf` / link | `index.html` — search for `VENMO_URL` and `venmo.com/u/turnepf` | Your tip jar of choice, or delete the `tip-jar-*` blocks (about 30 lines total) |
| Title "ScoringSpades.com" | `index.html` `<title>` and header, `how-to-play.html` `<title>` and header | Your name |
| App name "ScoringSpades" / "Spades" | `manifest.json` (`name`, `short_name`) | Your name |

The CSP in `_headers` allowlists Google's tag manager domains for gtag. If you remove GA, you can tighten the CSP by removing `https://www.googletagmanager.com` and `https://www.google-analytics.com` from `script-src` and `connect-src`. If you swap to a different analytics vendor, allowlist their domains instead.

### 2. Deploy

Pick whichever host you like. Here's the Cloudflare Workers path I use, but anything serving these files works.

**Cloudflare Workers (with Static Assets):**
```sh
# One-time: install wrangler and log in
npm install -g wrangler
wrangler login

# Edit the worker name in wrangler.jsonc (it's `scoringspades` here — change it)
# Then deploy from the repo root:
wrangler deploy
```

**Anything else:** point the host at the repo root. The 5 files that need to ship are `index.html`, `how-to-play.html`, `manifest.json`, `icon.svg`, and `_headers` (Cloudflare-specific; other hosts have their own equivalent). The `_headers` file is what wires up the CSP and HSTS — without it (or its equivalent on your host), you lose the security-header defenses but the app still works.

### 3. Custom domain

Whatever host you pick, point your domain at it per their docs. The app uses only relative URLs internally, so it'll work from any origin without code changes.

## Tip jar

If you enjoy the app, the tip jar on the live site is a Venmo link to [@turnepf](https://venmo.com/u/turnepf). No pressure. (If you fork the app, swap the link to your own as described above.)

## License

MIT — see [LICENSE](LICENSE).
