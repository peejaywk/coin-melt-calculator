# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## English Coin Melt Value Calculator

A dark-mode single-page web app that calculates the spot melt value of English gold and silver coins in GBP. Built with vanilla HTML, CSS, and JavaScript — no frameworks or build step.

**Live site:** https://coin-melt-calculator.onrender.com

## Files

| File | Purpose |
|------|---------|
| `index.html` | Entire app — HTML structure, CSS, and JavaScript in one file |
| `manifest.json` | PWA manifest (name, theme colour, icon, standalone display) |
| `sw.js` | Service worker — caches app shell for offline use; bypasses cache for price APIs |
| `icon.svg` | App icon used by the PWA manifest (Android/desktop install) |

## Running locally

```bash
python3 -m http.server 8080
# then open http://localhost:8080
```

Always serve over HTTP — opening as `file://` blocks live price fetches due to CORS.

## Architecture

The entire app lives in `index.html` as a single file: `<style>` block, HTML markup, then a `<script>` block. There is no build step, no bundler, and no test suite.

**Boot sequence** (`DOMContentLoaded`): `buildGoldGrid()` → `buildSilverGrid()` → `wireEraButtons()` → `fetchPrices()` → `generateAppleTouchIcon()`

**State:** Two module-level variables (`goldPerGram`, `silverPerGram`) hold current spot prices in GBP/gram. All melt values are derived from these on demand.

**Coin data** is defined as constants (`GOLD_COINS`, `SILVER_COINS`, `ERA`) at the top of the script block. Weights are from Royal Mint and Coinage Act specifications. All calculations use `price / 31.1035` (grams per troy oz) internally.

## Key JavaScript functions

| Function | What it does |
|----------|-------------|
| `fetchPrices()` | Orchestrates API fetch with fallback chain; shows manual input panel on total failure |
| `tryFromCoinbase()` | Primary price source — `XAU`/`XAG` exchange rates from Coinbase API |
| `tryFromGoldPriceOrg()` | Fallback price source — goldprice.org widget API |
| `updateAllMeltValues()` | Recalculates and updates every coin card's melt value display |
| `renderSpotPrices()` | Updates the gold/silver per-gram and per-troy-oz display in the price bar |
| `refreshSilverWeights()` | Updates displayed fine-silver weights when era selector changes; calls `updateAllMeltValues()` |
| `buildGoldGrid()` / `buildSilverGrid()` | Renders coin cards into the DOM |

## DOM ID conventions

Coin card elements use prefixed IDs derived from `coin.id`:
- `gc-{id}` — gold coin card wrapper
- `sc-{id}` — silver coin card wrapper
- `melt-{id}` — melt value `<span>` on each card (shared prefix for gold and silver)
- `sw-{id}` — fine silver weight `<span>` on silver cards

## Live price feeds

Coinbase is the sole source; manual input panel shown on failure:

- **Coinbase** — single request: `https://api.coinbase.com/v2/exchange-rates?currency=XAG`. `rates.GBP` gives silver price (GBP/oz); gold is derived via the XAU cross-rate: `rates.GBP / rates.XAU`. CORS-enabled, no API key.

The service worker (`sw.js`) explicitly bypasses its cache for this API URL so prices are always fresh.

## Design notes

- Dark theme via CSS custom properties (`--bg`, `--gold`, `--silver`, etc.) defined in `:root`
- Responsive breakpoints: 640 px (tablet), 520 px (mobile — stacked price bar, 2-column grid, 44 px touch targets), 360 px (single column)
- `viewport-fit=cover` and `env(safe-area-inset-*)` for notched phones
- `font-size: 16px` on number inputs prevents iOS auto-zoom
- Apple touch icon generated at runtime via `<canvas>` and injected into `<link rel="apple-touch-icon">`
- Silver tab is the default active tab on load
