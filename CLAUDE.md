# English Coin Melt Value Calculator

A dark-mode single-page web app that calculates the spot melt value of English gold and silver coins in GBP. Built with vanilla HTML, CSS, and JavaScript — no frameworks or build step.

## Files

| File | Purpose |
|------|---------|
| `index.html` | Entire app — HTML structure, CSS, and JavaScript in one file |
| `manifest.json` | PWA manifest (name, theme colour, icon, standalone display) |
| `sw.js` | Service worker — caches app shell for offline use; bypasses cache for price APIs |
| `icon.svg` | App icon used by the PWA manifest (Android/desktop install) |

## Running locally

```bash
python3 -m http.server 8080 --directory /home/peejaywk/Projects/price-calculator
# then open http://localhost:8080
```

Opening as `file://` may block live price fetches due to CORS. Always serve over HTTP.

## Installing as a mobile web app

The app is a PWA. Serve it over HTTP/HTTPS, then:
- **iOS Safari**: Share → Add to Home Screen
- **Android Chrome**: three-dot menu → Add to Home Screen (or prompted automatically)

## Coin data

All weights are sourced from Royal Mint and Coinage Act specifications and are defined as constants at the top of the `<script>` block in `index.html`.

### Gold coins (all 22 carat, 916.7‰)

| Coin | Gross (g) | Fine gold (g) |
|------|-----------|---------------|
| Sovereign (£1) | 7.9881 | 7.3224 |
| Half-Sovereign (10/–) | 3.9940 | 3.6612 |
| Quarter-Sovereign (5/–) | 1.9940 | 1.8278 |
| Double Sovereign (£2) | 15.9762 | 14.6448 |
| Five-Pound (£5) | 39.9405 | 36.6120 |

### Silver coins

Two era options, same gross weights:

| Coin | Gross (g) | Pre-1920 fine (g) @ 92.5% | 1920–1946 fine (g) @ 50% |
|------|-----------|---------------------------|--------------------------|
| Crown (5/–) | 28.2760 | 26.1553 | 14.1380 |
| Half-Crown (2/6) | 14.1380 | 13.0777 | 7.0690 |
| Florin (2/–) | 11.3104 | 10.4621 | 5.6552 |
| Shilling (1/–) | 5.6552 | 5.2311 | 2.8276 |
| Sixpence (6d) | 2.8276 | 2.6155 | 1.4138 |
| Threepence (3d) | 1.4138 | 1.3078 | 0.7069 |

## Live price feeds

Prices are fetched at page load and on demand via the Refresh button. Two sources are tried in order:

1. **Coinbase exchange-rate API** (primary) — `https://api.coinbase.com/v2/exchange-rates?currency=XAU` and `XAG`. Returns GBP per troy oz. CORS-enabled, no API key required.
2. **GoldPrice.org widget API** (fallback) — `https://data-asg.goldprice.org/dbXRates/GBP`. Returns GBP per troy oz directly.

If both fail, a manual input panel appears accepting GBP per troy oz for each metal.

All internal calculations use **GBP per gram** (`price / 31.1035`). The price bar displays both per-gram and per-troy-oz for reference.

## Key JavaScript functions

| Function | What it does |
|----------|-------------|
| `fetchPrices()` | Orchestrates API fetch with fallback chain |
| `updateAllMeltValues()` | Recalculates and updates every coin card's melt value |
| `renderSpotPrices()` | Updates the gold/silver per-gram and per-troy-oz display in the price bar |
| `refreshSilverWeights()` | Updates displayed fine-silver weights when era selector changes; calls `updateAllMeltValues()` |
| `buildGoldGrid()` / `buildSilverGrid()` | Renders coin cards into the DOM |

## Design notes

- Dark theme using CSS custom properties (`--bg`, `--gold`, `--silver`, etc.) defined in `:root`
- Responsive at 640 px (tablet), 520 px (mobile — stacked price bar, 2-column grid, 44 px touch targets), and 360 px (single column)
- `viewport-fit=cover` and `env(safe-area-inset-*)` used for notched phones
- iOS `font-size: 16px` on number inputs to prevent auto-zoom
- Apple touch icon generated at runtime via `<canvas>` and injected into `<link rel="apple-touch-icon">`
