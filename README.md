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

A single `index.html` file. No build step, no dependencies, no framework. Vanilla JS with a tiny custom render helper. A companion `how-to-play.html` page covers the rules.

## Deploy

Hosted on Cloudflare Pages (direct upload). Deploy instructions live in `CLAUDE.md`.

## Tip jar

If you enjoy the app, the tip jar is a Venmo link to [@turnepf](https://venmo.com/u/turnepf). No pressure.
