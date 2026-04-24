# English Coin Melt Value Calculator

A dark-mode web app that calculates the spot melt value of English gold and silver coins in GBP, using live metal prices.

**[Live site →](https://coin-melt-calculator.onrender.com)**

## Features

- **Live spot prices** fetched automatically in GBP, displayed per gram and per troy oz
- **Gold coins** — Sovereign, Half-Sovereign, Quarter-Sovereign, Double Sovereign, Five-Pound
- **Silver coins** — Crown, Half-Crown, Florin, Shilling, Sixpence, Threepence
- **Two silver eras** — Pre-1920 (92.5% silver) and 1920–1946 (50% silver)
- Melt values shown on every coin card simultaneously — no clicking required
- Manual price entry fallback if live prices are unavailable
- Installable as a **PWA** on iOS and Android (Add to Home Screen)
- Fully responsive — works on desktop and mobile

## Coin weights

All weights are taken from Royal Mint and Coinage Act specifications. Gold coins are 22 carat (916.7‰ fine). Silver coin weights are fixed; purity depends on the era selected.

## Running locally

```bash
python3 -m http.server 8080 --directory /path/to/project
```

Then open `http://localhost:8080`. Serving over HTTP (rather than opening the file directly) is needed for the live price fetch to work.

## Tech stack

Vanilla HTML, CSS, and JavaScript — no frameworks, no build step. Single `index.html` file plus a PWA manifest and service worker.

## Price sources

1. [Coinbase exchange-rate API](https://api.coinbase.com) (primary) — no API key required
2. [GoldPrice.org](https://goldprice.org) widget API (fallback)
3. Manual entry if both are unavailable

## Disclaimer

Melt values are indicative only. They do not account for fabrication premium, numismatic value, dealer spread, or any costs associated with melting. Not financial advice.
