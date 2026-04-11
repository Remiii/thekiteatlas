# TheKiteAtlas

[![Live](https://img.shields.io/badge/live-thekiteatlas.com-blue)](https://thekiteatlas.com)

Interactive guide to the best kitesurf spots worldwide, month by month.

## Overview

TheKiteAtlas lists 46 kite spots (74 monthly entries) across 12 months, with wind conditions, air/water temperatures, required level and water type for each spot. The site is bilingual (French/English) and filterable by spot type (flat, waves, beginner, tropical, warm/cold water, Europe…).

## Stack

- Vanilla HTML / CSS / JS — zero frameworks, zero dependencies
- Spot data externalized in `spots.json`
- Fonts: [Playfair Display](https://fonts.google.com/specimen/Playfair+Display) + [DM Sans](https://fonts.google.com/specimen/DM+Sans) (SIL Open Font License)
- Hosted on GitHub Pages

## Files

```
index.html    ← single page, CSS and JS embedded
spots.json    ← data for all 46 spots × 12 months (74 monthly entries)
CNAME         ← GitHub Pages custom domain
README.md     ← this file
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
    "fr": ["15-25 kts", "flat", "débutant", "freeride"],
    "en": ["15-25 kts", "flat", "beginner", "freeride"]
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

Available filters: `flat`, `wave`, `beginner`, `europe`, `tropical`, `warm` (water ≥ 23°C), `cold` (water < 20°C).

## Contributing

Spotted a missing spot or an error? Contributions are welcome.

1. Fork the repo
2. Edit `spots.json` — add or update a spot following the structure above
3. Open a pull request with a short description of the change

Please keep one spot per PR to make review easier.
