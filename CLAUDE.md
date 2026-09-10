# Pokemon Collection Tracker

## Project Overview
Multi-collection web app tracking Pokemon TCG cards across three collections. Deployed to GitHub Pages.

**Live site:** https://gazzahaas.github.io/Pokemon-Collection/
**Deploy branch:** `claude/markdown-file-build-rdxkry`
**Dev branch:** `claude/continue-previous-session-frew3s`

## File Structure
- `index.html` — Landing page with 3 collection tiles showing live progress from localStorage
- `komiya.html` — Tomokazu Komiya collection (278 cards, all eras)
- `kanda.html` — Shinji Kanda collection (33 cards, eras 0/1/8)
- `gholdengo.html` — Gholdengo & Gimmighoul collection (16 cards, eras 0/8, Pokemon sub-filter)

## Architecture
- Separate HTML files per collection — all HTML, CSS, and JS embedded (no build tools)
- Each collection has its own localStorage key (`komiya-tcg`, `kanda-tcg`, `gholdengo-tcg`)
- Additional per-collection keys for filter state: `{key}-filter`, `{key}-era`, and `gholdengo-tcg-pokemon`
- Dark theme with Pokemon yellow (#ffcb05) accent, green (#3ecf8e) for collected state
- Responsive grid: 2 columns mobile / 3 tablet / 4 desktop

## Image Sources
- **Primary:** pokemontcg.io CDN — `https://images.pokemontcg.io/{set_id}/{local_id}_hires.png`
- **Fallback:** Serebii — `https://www.serebii.net/card/{folder}/{num}.jpg`
- Serebii image URLs need leading zeros and letter prefixes stripped (handled in `serebiiImg()`)
- Serebii page URLs (.shtml) use the ORIGINAL padded numbers (handled in `serebiiPage()`)
- NO inline `onerror` on img tags — all error handling via JS event listeners in `setupImage()`
- 2 Komiya cards have no images (Miscellaneous Promos 1998: Farfetch'd, Touch Generation Turn!) — show placeholder

## Key Data Structures (in JS, per collection file)
- `SET_IDS` — maps display set names to pokemontcg.io set IDs (e.g., `"Surging Sparks":"sv8"`)
- `CARDS` — array of entries: `[name, cardNum, setName, serebiiFolder, serebiiNum, eraIndex]`
  - Gholdengo adds a 7th element: `pokemonTag` ("gholdengo" or "gimmighoul")
- `ERA_NAMES` — era display names (sparse array — only populated indices used per collection)
- `NORMAL_ONLY_SETS` — sets where cards have no reverse holos (promos, Neo era, Japanese sets)
- `HOLO_RARES` — cards verified as "Rare Holo" (Komiya has 13; Kanda/Gholdengo have none)

## Variant Tracking
- Bitmask-based: 1=Normal, 2=Holo, 4=Reverse Holo
- `getVariants(card)` returns variant type: 0=Normal only, 1=Normal+Reverse Holo, 3=Holo+Reverse Holo
- Era 8 (Japanese exclusives) and NORMAL_ONLY_SETS always return type 0
- Cards numbered above set total (secret rares) return type 0
- Special Pokemon (V, GX, VMAX, VSTAR, ex, V-UNION) return type 0
- HOLO_RARES return type 3 (Holo + Reverse Holo, no non-holo version)
- Komiya has migration logic converting old localStorage values for holo rares (value 1 → 2)

## Filters (per collection page)
- **Text search** — filters by Pokemon name or set name
- **Status filter** — All / Collected / Needed buttons, persisted to localStorage
- **Era dropdown** — filters by era, only shows eras with cards in that collection
- **Pokemon sub-filter** (Gholdengo only) — All / Gholdengo / Gimmighoul buttons

## Key Functions
- `cardId(c)` — returns `setName|cardNum`
- `getLocalId(num)` — derives pokemontcg.io local ID from card number
- `ptcgUrl(c)` — builds primary image URL (returns null if set not in SET_IDS)
- `serebiiImg(c)` — builds fallback image URL (strips letter prefixes and leading zeros)
- `serebiiPage(c)` — builds Serebii card info page URL (keeps original format)
- `setupImage(img, card, container)` — handles image loading with fallback chain
- `createCard(card)` — builds card DOM element with variant popup
- `getVariants(card)` — determines which print variants a card has
- `applyFilters()` — combines all active filters (text, status, era, pokemon)

## Sets Without pokemontcg.io IDs (Serebii-only images)
Storm Emeralda, 30th Celebration, 30th Celebration Japan, SM Promo, XY Promo, P Promos, Play Promotional, Miscellaneous Promos 1998, VS, Vending Machine Set 1/2/3, Mega Promos

## Collection Card Counts
- Komiya: 278 cards across 9 eras
- Kanda: 33 cards across 3 eras (0=SV, 1=SWSH, 8=Japanese)
- Gholdengo: 16 cards across 2 eras (0=SV, 8=Japanese) — 9 Gholdengo + 7 Gimmighoul
