# API Reference

**Base URL**: `https://berghain.ravers.workers.dev`

## Features

- **CORS enabled** — works from any origin
- **No authentication** — free and open for human users
- **Content negotiation** — HTML pages return markdown with `Accept: text/markdown`
- **Special character support** — handles characters like Ø, ø, À-ÿ, etc.
- **Fast responses** — <100ms average with >95% cache hit rate
- **Free & open** — every endpoint below is free for everyone, including AI crawlers; only bulk exports and flyer PDFs are paid via the [x402 protocol](x402-ai-paywall.md)

## Endpoints

### Database Statistics

#### `GET /api/stats`

Overall database statistics including total counts and venue breakdown.

```json
{
  "total_artists": 2515,
  "total_events": 1030,
  "total_performances": 13204,
  "venue_breakdown": [
    { "venue": "Berghain", "count": 6763 },
    { "venue": "Panorama Bar", "count": 6441 }
  ]
}
```

#### `GET /api/stats/monthly`

Monthly performance statistics.

| Parameter | Type | Default | Description |
| --- | --- | --- | --- |
| `year` | integer | current year | Year to analyze |

```json
[
  {
    "month": 1,
    "event_count": 62,
    "total_artists": 758,
    "avg_artists_per_event": 12.23
  }
]
```

### Artists

#### `GET /api/artists`

Search and browse all artists with pagination.

| Parameter | Type | Default | Description |
| --- | --- | --- | --- |
| `search` | string | — | Search term (supports special characters) |
| `page` | integer | 1 | Page number |
| `limit` | integer | 20 | Results per page |

```bash
curl "https://berghain.ravers.workers.dev/api/artists?search=rødhåd"
```

#### `GET /api/artists/:id`

Artist details by numeric ID.

#### `GET /api/artists/by-name/:name`

Find artist by exact name (URL-encoded).

#### `GET /api/artists/:id/stats`

Detailed statistics for an artist including career span.

```json
{
  "name": "Ben Klock",
  "total_performances": 197,
  "berghain_performances": 186,
  "panorama_performances": 11,
  "first_performance": "2005-01-15",
  "last_performance": "2026-06-13",
  "active_years": 22,
  "recent_performances_2years": 23
}
```

#### `GET /api/artists/:id/performances`

Full performance history for an artist.

```json
[
  {
    "title": "Klubnacht",
    "date": "04.01.2026",
    "iso_date": "2026-01-04",
    "url": "https://www.berghain.berlin/de/event/79420/",
    "venue": "Berghain",
    "artist_name": "Ben Klock"
  }
]
```

### Rankings

#### `GET /api/artists/ranking`

Artists ranked by total performance count.

| Parameter | Type | Default | Description |
| --- | --- | --- | --- |
| `limit` | integer | 50 | Number of results (max 500) |

```json
[
  {
    "id": 14,
    "name": "Norman Nodge",
    "total_performances": 149,
    "berghain_performances": 142,
    "panorama_performances": 7
  }
]
```

#### `GET /api/artists/ranking/year/:year`

Rankings for a specific year.

### Events

#### `GET /api/shows`

Browse all Klubnacht events.

| Parameter | Type | Default | Description |
| --- | --- | --- | --- |
| `limit` | integer | 20 | Number of results |
| `year` | integer | — | Filter by year |

```json
[
  {
    "id": 912,
    "event_id": 79500,
    "title": "Klubnacht",
    "date": "Saturday 28.02.2026 start 00:00",
    "iso_date": "2026-02-28",
    "year": 2026,
    "month": 2,
    "url": "https://www.berghain.berlin/de/event/79500/",
    "total_artists": 14,
    "actual_performances": 14
  }
]
```

For 2004–2009 events the `url` points to the source flyer PDF (e.g. `https://www.berghain.berlin/documents/110/berghain-flyer-2005-12.pdf`) rather than an event page. The full lineup for a single event is available as an HTML/Markdown page at `/shows/:eventId` (there is no JSON endpoint for a single event).

### Residents

#### `GET /api/residents/current`

Current active residents based on 3-year rolling performance analysis (minimum 2 shows in the last year).

```json
[
  {
    "id": 36,
    "name": "Steffi",
    "total_performances_3y": 32,
    "recent_performances_1y": 10,
    "last_performance": "2026-02-28",
    "total_performances": 125,
    "berghain_performances": 58,
    "panorama_performances": 67
  }
]
```

### Other Endpoints

| Endpoint | Description |
| --- | --- |
| `GET /api/years` | List of available years in the database |
| `GET /api/period` | Data coverage date range |
| `GET /api/flyers` | Index of the 2004–2009 flyer archive (free; the PDFs themselves are paid) |

### Bulk Export (paid via x402)

Full-table dumps. Support `?format=json` (default) or `?format=csv`. **$0.10 per request for everyone** (not only AI crawlers) via [x402](x402-ai-paywall.md).

| Endpoint | Description |
| --- | --- |
| `GET /api/export/artists` | Export all artists |
| `GET /api/export/events` | Export all events |
| `GET /api/export/performances` | Export all performances |

### Historical Flyers (2004–2009)

| Endpoint | Description |
| --- | --- |
| `GET /flyers/berghain-flyer-YYYY-MM.pdf` | Original monthly flyer PDF — **$0.01 per request** via x402 |

### Machine-Readable Discovery

| Endpoint | Description |
| --- | --- |
| `GET /openapi.json` | OpenAPI 3 specification (with x402 price annotations) |
| `GET /.well-known/x402` | x402 payment manifest (routes, pricing, wallet) |
| `GET /.well-known/agentic-capabilities.json` | Agent capability descriptor |

### Discovery and Feeds

| Endpoint | Description |
| --- | --- |
| `GET /llms.txt` | LLM discovery file (summary) |
| `GET /llms-full.txt` | LLM discovery file (full API docs) |
| `GET /sitemap.xml` | XML sitemap for search engines |
| `GET /feed.xml` | RSS feed of recent events |
| `GET /robots.txt` | Robots directives |
| `GET /health` | API health check |

### Public Crawl Statistics

| Endpoint | Description |
| --- | --- |
| `GET /api/public/crawl-summary?days=N` | Aggregated AI crawler statistics (max 90 days) |
| `GET /dashboard/crawl-stats` | Visual crawl stats dashboard |

These endpoints are publicly accessible without authentication.

## HTML Pages

| Path | Description |
| --- | --- |
| `/` | Homepage with overview statistics |
| `/current-residents` | Current resident DJs |
| `/artists/:slug` | Individual artist profile |
| `/shows/:eventId` | Historical event lineup |

All HTML pages support `Accept: text/markdown` for structured markdown responses.
