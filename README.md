# TheKiteAtlas

[![Live](https://img.shields.io/badge/live-thekiteatlas.com-blue)](https://thekiteatlas.com)

Interactive guide to the best kitesurf spots worldwide, month by month.

## Overview

TheKiteAtlas lists 46 kite spots (74 monthly entries) across 12 months, with wind conditions, air/water temperatures, required level and water type for each spot. The site is bilingual (French/English) and filterable by spot type (flat, waves, beginner, tropical, warm/cold water, Europe…).

Spot pages provide detailed guides: wind probability by month, sub-spots, accommodations, activities, practical info and budget estimator.

## Stack

- Vanilla HTML / CSS / JS — zero frameworks, zero dependencies
- Shared stylesheet in `style.css`, page-specific styles inline per page
- Spot data in `spots.json`
- Fonts: [Playfair Display](https://fonts.google.com/specimen/Playfair+Display) + [DM Sans](https://fonts.google.com/specimen/DM+Sans) (SIL Open Font License)
- Hosted on GitHub Pages

## Files

```
index.html        ← home page (spot list, month/filter navigation)
style.css         ← shared design system (variables, tags, layout base)
spots.json        ← data for all 46 spots × 12 months (74 monthly entries)
spots/
  dakhla.html     ← full spot guide: Dakhla, Morocco
CNAME             ← GitHub Pages custom domain
README.md         ← this file
CLAUDE.md         ← project context for AI-assisted development
```

## Local development

The site loads `spots.json` via `fetch()`, which requires an HTTP server (browsers block `fetch` on `file://`).

```bash
# Clone the repo
git clone git@github.com:Remiii/thekiteatlas.git
cd thekiteatlas

# Start a local server
python3 -m http.server 8000

# Open http://localhost:8000
```

## Adding / editing a spot

Edit `spots.json`. Each spot follows this structure:

```json
{
  "name": "Spot name",
  "country": { "fr": "Pays", "en": "Country" },
  "region": { "fr": "Région", "en": "Region" },
  "tags": {
    "fr": [["15-25 kts"], ["flat"], ["débutant"], ["freeride"]],
    "en": [["15-25 kts"], ["flat"], ["beginner"], ["freeride"]]
  },
  "description": {
    "fr": "Description en français.",
    "en": "English description."
  },
  "air_temp": 28,
  "water_temp": 26,
  "avg_wind_kts": 20,
  "wind_score": 4,
  "filters": ["tropical", "flat", "beginner", "warm"]
}
```

### Tags

Each spot has 4 tag groups. Each group is an array (supports multiple values).

| # | Color | Meaning | EN values | FR values |
|---|-------|---------|-----------|-----------|
| 1 | Purple | Wind range | `10-20 kts`, `15-25 kts`, `20-30 kts`, `25-35 kts` | same |
| 2 | Teal | Water type | `flat`, `waves`, `choppy` | `flat`, `vagues`, `clapot` |
| 3 | Orange | Level | `beginner`, `intermediate`, `advanced` | `débutant`, `intermédiaire`, `confirmé` |
| 4 | Sky blue | Ride style | `freeride`, `freestyle`, `wave ride` | same |

Multi-value example: a spot with flat water and waves uses `["flat", "waves"]` for tag 2.

### Filters

Filters are displayed grouped on the home page and are combinable (AND logic).

| Group | Filter keys | Source |
|-------|-------------|--------|
| Wind | `10-20 kts`, `15-25 kts`, `20-30 kts`, `25-35 kts` | Tag 1 |
| Water | `flat`, `waves`, `choppy` | Tag 2 |
| Level | `beginner`, `intermediate` | Tag 3 (inclusive) |
| Style | `freeride`, `freestyle`, `wave ride` | Tag 4 |
| Conditions | `warm` (water ≥ 23°C), `cold` (water < 20°C), `europe`, `tropical` | Computed / region |

Level filters are inclusive: `beginner` shows only beginner spots, `intermediate` shows beginner + intermediate spots. No `advanced` filter needed (all spots).

## Adding a spot page

1. Create `spots/<slug>.html` — link `../style.css`, add page-specific CSS inline
2. Register the slug in `index.html` → `renderSpotCard()` → `spotPage` object:
   ```js
   const spotPage = { "Dakhla": "spots/dakhla.html", "New Spot": "spots/new-spot.html" }[spot.name];
   ```

## Contributing

Spotted a missing spot or an error? Contributions are welcome.

1. Fork the repo
2. Edit `spots.json` — add or update a spot following the structure above
3. Open a pull request with a short description of the change

Please keep one spot per PR to make review easier.
