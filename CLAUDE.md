# CLAUDE.md — TheKiteAtlas

## Project vision

TheKiteAtlas aims to become a large-scale kitesurf destination guide (200+ spots, blog, videos, user contributions, maps, marketplace). The current version is a deliberate starting point — simple and fast — but every decision should leave room for growth.

## Current state

- 46 unique kite spots, 74 monthly entries (some spots appear in multiple months)
- Home page: `index.html` — spot list with month navigation and filters
- Shared stylesheet: `style.css` — design system (variables, tags, footer, animations)
- First spot page: `spots/dakhla.html` — full bilingual guide (wind, spots, accommodations, activities, practical, budget)
- Bilingual: French and English, auto-detected from browser language
- Hosted on GitHub Pages with a custom domain (thekiteatlas.com)
- Deployment is automatic on push to `main`

## File structure

```
index.html          ← home: spot list, month nav, filters
style.css           ← shared CSS (variables, tags, base layout)
spots.json          ← spot data (months > spots structure — to be migrated)
spots/
  dakhla.html       ← spot page: Dakhla
CNAME
CLAUDE.md
README.md
```

## CSS architecture

- `style.css` contains all shared styles: CSS variables, reset, body, grain overlay, language switcher, tags, wind rating bars, footer base, `fadeUp` animation
- Each page has a `<style>` block with only its specific styles
- New spot pages must link `../style.css` and keep only page-specific CSS inline
- The `.ls` language switcher is positioned `absolute` on the home page (override in `index.html`), and inside `.topbar` on spot pages (no override needed)

## Adding a spot page

1. Create `spots/<slug>.html`
2. Link `<link rel="stylesheet" href="../style.css" />`
3. Register the slug in `index.html` → `renderSpotCard()` → `spotPage` object

## Target data model

The data is a **graph** of nodes connected by edges. This is the model to migrate toward — even while data lives in a static JSON file.

### Node types

**`spot`** — a real geographical location. Has kite data:

```json
{
  "id": "dakhla-lagune",
  "type": "spot",
  "slug": "dakhla/lagune",
  "name": { "fr": "Lagune", "en": "Lagoon" },
  "country": { "fr": "Maroc", "en": "Morocco" },
  "region": { "fr": "Afrique", "en": "Africa" },
  "gps": { "lat": 23.71, "lng": -15.94 },
  "description": { "fr": "...", "en": "..." },
  "tags": { "fr": ["15-25 kts", "flat"], "en": ["15-25 kts", "flat"] },
  "air_temp": 28,
  "water_temp": 22,
  "avg_wind_kts": 20,
  "wind_score": 4,
  "filters": ["flat", "beginner", "warm"],
  "months": ["jan", "feb", "mar", "nov", "dec"]
}
```

**`collection`** — a thematic or editorial grouping (no kite data, container only):

```json
{
  "id": "best-beginner-spots",
  "type": "collection",
  "slug": "collections/best-beginner-spots",
  "name": { "fr": "Meilleurs spots débutants", "en": "Best beginner spots" }
}
```

### Edges

Edges connect nodes. For now edges carry no data — they are just relations:

```json
{
  "edges": [
    { "from": "dakhla", "to": "dakhla-lagune", "relation": "contains" },
    { "from": "best-beginner-spots", "to": "dakhla-lagune", "relation": "includes" }
  ]
}
```

### Key design rules

- Nesting is **theoretically unlimited** — a spot can contain spots, which can contain spots
- A node can have **multiple parents** (graph, not a tree)
- `months` is an attribute of a spot node — not the top-level structure
- Collections are containers only: no kite data, no GPS
- Edges are relations only: no data for now, extensible later
- Slugs are human-readable and URL-friendly (`dakhla/lagune`)

### Migration note

The current `spots.json` uses a `months > spots` structure (inverted). It must be migrated to a `nodes + edges` flat structure where months are attributes of spot nodes. Do not extend the old structure — migrate it.

## Development rules

- Code and comments must be in **English**
- Vanilla JS for now — React migration planned later, no date set
- No backend yet — all data is static JSON. This will evolve toward a database — likely NoSQL/graph-oriented (e.g. Neo4j, ArangoDB) given the graph data model.
- Work directly on `main` — deployment to GitHub Pages is automatic on push
- No separate dev branch for now

## Design

- The site must look great — visual quality matters as much as content
- Fully responsive: mobile experience is a first-class citizen, not an afterthought

## What to preserve as the site grows

- Bilingual support (FR/EN) must be maintained across all new features
- The graph data model is the source of truth — extend nodes/edges, do not invent parallel structures
- Performance matters: the site should stay fast even with 200+ spots
