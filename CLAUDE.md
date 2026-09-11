# Pokemon Collection Tracker

## Project Overview
Mobile-first web app tracking Pokemon TCG cards. Deployed to GitHub Pages.

**Live site:** https://gazzahaas.github.io/Pokemon-Collection/
**Branch:** `claude/markdown-file-build-rdxkry` (GitHub Pages deploys from here)

## IMPORTANT — Keep it simple
- Do the obvious thing first. Don't overcomplicate.
- Need a card image? Web search for it. Don't guess URLs.
- Need to adjust a tile? Crop the image or change one CSS value. Check it. Move on.
- Two separate builds: Build 1 (komiya, kanda, gholdengo) and Build 2 (snorlax, munchlax)

### Past mistakes — don't repeat these
1. **Card images**: Spent ages programmatically guessing URLs and writing fallback chains when a simple web search for each card would have found every image in minutes.
2. **Tile positioning**: Spent many iterations fighting CSS (height: 220% → 300% → 400%, top offsets, transforms) trying to zoom out a portrait image in a landscape tile, not realizing `object-fit: cover` scales by width regardless of element height. Fix was trivial: crop the source image to landscape and use one `object-position` value.

## File Structure
- `index.html` — Landing page with 3 image-only tiles (names baked into artwork images)
- `komiya.html` — Tomokazu Komiya collection (278 cards, all eras)
- `kanda.html` — Shinji Kanda collection (33 cards, eras 0/1/8)
- `gholdengo.html` — Gholdengo & Gimmighoul collection (16 cards, eras 0/8, Pokemon sub-filter)
- `snorlax-cards.html` — Snorlax collection (63 cards, Build 2)
- `munchlax.html` — Munchlax collection (14 cards, Build 2)

## Architecture
- Mobile-first design (forget about desktop for now)
- Separate HTML files per collection — all HTML, CSS, and JS embedded (no build tools)
- Each collection has its own localStorage key (`komiya-tcg`, `kanda-tcg`, `gholdengo-tcg`, `snorlax-tcg`, `munchlax-tcg`)
- Dark theme with Pokemon yellow (#ffcb05) accent, green (#3ecf8e) for collected state
- Responsive grid: 2 columns mobile / 3 tablet / 4 desktop
- Build 1 cards are `<div>` elements; Build 2 cards are `<a>` links to Serebii

## Landing Page (index.html)
- 3 tiles linking to Build 1 collection pages (NOT snorlax/munchlax — separate build)
- All tiles 180px height with `border-radius: 14px` and `overflow: hidden`
- Pokeball background at 0.3 opacity via `body::before` pseudo-element
- Tile images are pre-cropped to frame text correctly
- Komiya tile: `object-position: center 61%`
- Kanda tile: `object-position: center 55%`
- Gholdengo tile: `object-position: center 78%` (shows "Make It Rain" text)

## Collection Page Backgrounds
- `komiya.html` — `images/komiya-bg.jpg` (Psyduck Scream art), `center center`, opacity 0.3
- `kanda.html` — `images/kanda-bg.jpg` (Magikarp waterfall art), `center 70%`, opacity 0.3
- `gholdengo.html` — `images/gholdengo-bg.jpg` (cropped Gholdengo close-up), `center center`, opacity 0.3

## Images Directory
- **Card images (local overrides):** farfetchd-corocoro-1998.jpg, touch-generation-1998.jpg, gimmighoul-30th-067.webp, gholdengo-30th-087.jpg
- **Tile artwork:** komiya-tile.jpg (863x713), kanda-tile.jpg (960x553), gholdengo-tile.jpg (900x539) — pre-cropped landscape crops
- **Backgrounds:** pokeball-bg.jpg (index only), komiya-bg.jpg, kanda-bg.jpg, gholdengo-bg.jpg

## Image Sources
- **Build 1 chain:** OVERRIDE_IMAGES → pokemonComImg (pokemon.com CDN) → ptcgUrl (pokemontcg.io)
- **Build 2 chain:** OVERRIDE_IMAGES (snorlax only) → ptcgUrl (pokemontcg.io) → serebiiImg (serebii.net)
- **Card-back detection:** `img.naturalWidth === 640 && img.naturalHeight === 892`
- NO inline `onerror` on img tags — all error handling via JS event listeners in `setupImage()`
- **0 placeholder cards** — all cards across all collections have images

## Key Data Structures (in JS, per collection file)
- `SET_IDS` — maps display set names to pokemontcg.io set IDs
- `CARDS` — array of entries: `[name, cardNum, setName, serebiiFolder, serebiiNum, eraIndex]`
  - Gholdengo adds 7th element: `pokemonTag` ("gholdengo" or "gimmighoul")
- `ERA_NAMES` — era display names (sparse array)
- `NORMAL_ONLY_SETS` — sets where cards have no reverse holos (Build 1 only where needed)
- `HOLO_RARES` — cards verified as "Rare Holo" (Komiya: 13, Snorlax: 7, others: removed as empty)
- `OVERRIDE_IMAGES` — map of `cardId` → image URL for cards without CDN sources

## Variant Tracking
- Bitmask-based: 1=Normal, 2=Holo, 4=Reverse Holo (Build 2 adds 8=1st Edition, 16=Unlimited)
- `getVariants(card)` returns variant type: 0=Normal only, 1=Normal+Reverse Holo, 3=Holo+Reverse Holo
- Komiya has migration logic converting old localStorage values for holo rares (value 1 → 2)

## Filters (per collection page)
- **Text search** — filters by Pokemon name or set name
- **Status filter** — All / Collected / Needed buttons, persisted to localStorage
- **Era dropdown** — filters by era
- **Pokemon sub-filter** (Gholdengo only) — All / Gholdengo / Gimmighoul buttons

## OVERRIDE_IMAGES Counts
- Komiya: 34 entries (including Oricorio Crown Zenith)
- Kanda: 7 entries (Bulbapedia URLs)
- Gholdengo: 5 entries (3 Bulbapedia + 2 local paths)
- Snorlax: 2 entries (TCG Classic + Hungry Snorlax)
- Munchlax: 0 entries

## Collection Card Counts
- Komiya: 278 cards across 9 eras
- Kanda: 33 cards across 3 eras (0=SV, 1=SWSH, 8=Japanese)
- Gholdengo: 16 cards across 2 eras (0=SV, 8=Japanese) — 9 Gholdengo + 7 Gimmighoul
- Snorlax: 63 cards
- Munchlax: 14 cards

## Snorlax/Munchlax Landing Page (snorlax.html)
- Same structure as index.html: pokeball background, Pokemon logo header, image-only tiles
- Header subtitle: "Snorlax & Munchlax" (not "Collection Tracker")
- 2 tiles (150px height): snorlax-cards.html and munchlax.html
- Tile images: `images/snorlax-tile.jpg` and `images/munchlax-tile.jpg` (placeholders — user will add later)
- No back arrow (standalone entry point like index.html)
- No footer

## Global Styling Patterns (applied to ALL pages)

### Header Holo Shimmer Line
All pages use an animated holographic gradient line at the bottom of the header (replaces old `border-bottom: 2px solid var(--accent)`):
```css
header { border-bottom: none; }
header::after {
  content: "";
  position: absolute;
  bottom: 0; left: 0;
  width: 100%; height: 4px;
  background: linear-gradient(90deg,
    #ff6ec7 0%, #a855f7 12%, #6366f1 22%, #3b82f6 32%,
    #22d3ee 42%, #34d399 52%, #facc15 62%, #fb923c 72%,
    #f43f5e 82%, #c084fc 92%, #ff6ec7 100%);
  background-size: 200% 100%;
  animation: holo-shift 3s linear infinite;
}
@keyframes holo-shift {
  0% { background-position: 0% 0; }
  100% { background-position: 200% 0; }
}
```

### Pokemon Logo Header (landing pages only: index.html, snorlax.html)
```html
<h1><img src="images/pokemon-logo.svg" alt="Pokemon"><span>Collection Tracker</span></h1>
```
- Logo: `images/pokemon-logo.svg` (transparent SVG from Wikimedia Commons)
- Subtitle styled: `font-size: 0.85rem; font-weight: 300; color: rgba(255,255,255,0.7); letter-spacing: 2px; text-transform: uppercase`

### Zoom Disabled (all pages)
```html
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
```
```css
body { touch-action: pan-y; }
```

### Collection Page Header Design (komiya, kanda, gholdengo, snorlax-cards, munchlax)
- Back link: dim color (`var(--text-dim)`), transitions to accent on hover
- Title: `font-size: 1.35rem`, card count text: `font-size: 0.75rem`
- Progress bar: `height: 4px`, `background: rgba(255,255,255,0.08)`, `border-radius: 2px`
- Progress count: `font-size: 0.75rem`
- Search: `border-radius: 20px` (pill shape), `background: rgba(255,255,255,0.05)`, `border: 1px solid rgba(255,255,255,0.08)`
- Filter buttons: pill-shaped (`border-radius: 20px`), `padding: 0.3rem 0.7rem`, `font-size: 0.75rem`, transparent background
- Era dropdown: pill-shaped, `margin-left: auto` (pushed to right end of filter row)
- All filters in one `.filter-row` flex container (buttons + era dropdown inline)

### No Footers
All footers have been removed across every page.

## Code Cleanup Applied
- Removed dead `countOwnedVariants()` from komiya and kanda
- Removed empty `HOLO_RARES` from kanda, gholdengo, munchlax
- Removed empty `NO_PCOM_CDN` from gholdengo
- Removed empty `NORMAL_ONLY_SETS` from munchlax
- Trimmed kanda `NO_PCOM_CDN` to only `{"swshp":1}` (the one set actually used)
- Simplified snorlax `setupImage` from nested 3-branch to clean pattern matching Build 1
