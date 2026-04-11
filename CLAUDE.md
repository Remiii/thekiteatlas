# CLAUDE.md — TheKiteAtlas

## Project vision

TheKiteAtlas aims to become a large-scale kitesurf destination guide (200+ spots, blog, videos, user contributions, maps, marketplace). The current version is a deliberate starting point — simple and fast — but every decision should leave room for growth.

## Current state

- 46 unique kite spots, 74 monthly entries (some spots appear in multiple months)
- Single-page app: `index.html` (HTML/CSS/JS embedded) + `spots.json`
- Bilingual: French and English
- Filterable by spot type (flat, wave, beginner, europe, tropical, warm, cold)
- Hosted on GitHub Pages with a custom domain (thekiteatlas.com)

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
