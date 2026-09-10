# Komiya Pokemon TCG Collection Tracker

## Project Overview
Single-page web app tracking all 278 English Pokemon TCG cards illustrated by Tomokazu Komiya. Deployed to GitHub Pages.

**Live site:** https://gazzahaas.github.io/Pokemon-Collection/
**Branch:** `claude/markdown-file-build-rdxkry`

## Architecture
- Single `index.html` file — all HTML, CSS, and JS embedded (no build tools)
- localStorage persistence (key: `"komiya-tcg"`)
- Dark theme with Pokemon yellow (#ffcb05) accent, green (#3ecf8e) for collected state
- Responsive grid: 2 columns mobile / 3 tablet / 4 desktop

## Image Sources
- **Primary:** pokemontcg.io CDN — `https://images.pokemontcg.io/{set_id}/{local_id}_hires.png`
- **Fallback:** Serebii — `https://www.serebii.net/card/{folder}/{num}.jpg`
- Serebii image URLs need leading zeros and letter prefixes stripped (handled in `serebiiImg()`)
- Serebii page URLs (.shtml) use the ORIGINAL padded numbers (handled in `serebiiPage()`)
- NO inline `onerror` on img tags — all error handling via JS event listeners in `setupImage()`
- 2 cards have no images (Miscellaneous Promos 1998: Farfetch'd, Touch Generation Turn!) — show placeholder

## Key Data Structures (in JS)
- `SET_IDS` — maps display set names to pokemontcg.io set IDs (e.g., `"Surging Sparks":"sv8"`)
- `CARDS` — array of 278 entries: `[name, cardNum, setName, serebiiFolder, serebiiNum, eraIndex]`
- `ERA_NAMES` — 9 era display names
- `NORMAL_ONLY_SETS` — sets where cards have no reverse holos (promos, Neo era, Japanese sets)
- `HOLO_RARES` — 13 cards verified as "Rare Holo" via pokemontcg.io API

## Variant Tracking
- Bitmask-based: 1=Normal, 2=Holo, 4=Reverse Holo
- `getVariants(card)` returns variant type: 0=Normal only, 1=Normal+Reverse Holo, 3=Holo+Reverse Holo
- Era 8 (Japanese exclusives) and NORMAL_ONLY_SETS always return type 0
- Cards numbered above set total (secret rares) return type 0
- Special Pokemon (V, GX, VMAX, VSTAR, ex, V-UNION) return type 0
- HOLO_RARES return type 3 (Holo + Reverse Holo, no non-holo version)
- Migration logic converts old localStorage values for holo rares (value 1 → 2)

## Key Functions
- `cardId(c)` — returns `setName|cardNum`
- `getLocalId(num)` — derives pokemontcg.io local ID from card number
- `ptcgUrl(c)` — builds primary image URL (returns null if set not in SET_IDS)
- `serebiiImg(c)` — builds fallback image URL (strips letter prefixes and leading zeros)
- `serebiiPage(c)` — builds Serebii card info page URL (keeps original format)
- `setupImage(img, card, container)` — handles image loading with fallback chain
- `createCard(card)` — builds card DOM element with variant popup
- `getVariants(card)` — determines which print variants a card has

## Sets Without pokemontcg.io IDs (Serebii-only images)
Storm Emeralda, 30th Celebration, SM Promo, XY Promo, P Promos, Play Promotional, Miscellaneous Promos 1998, VS, Vending Machine Set 1/2/3
