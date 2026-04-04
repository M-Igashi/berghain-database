# API Reference

**Base URL**: `https://berghain.ravers.workers.dev`

## Features

- **CORS enabled** — works from any origin
- **No authentication** — free and open for human users
- **Content negotiation** — HTML pages return markdown with `Accept: text/markdown`
- **Special character support** — handles characters like Ø, ø, À-ÿ, etc.
- **Fast responses** — <100ms average with >95% cache hit rate
- **x402 paywall** — AI crawlers pay per request via [x402 protocol](x402-ai-paywall.md); humans are free

## Endpoints

### Database Statistics

#### `GET /api/stats`

Overall database statistics including total counts and venue breakdown.

```json
{
  "total_artists": 2099,
  "total_events": 748,
  "total_performances": 10466,
  "venue_breakdown": [
    { "venue": "Berghain", "count": 5581 },
    { "venue": "Panorama Bar", "count": 4885 }
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
  "total_performances": 138,
  "berghain_performances": 130,
  "panorama_performances": 8,
  "first_performance": "2009-12-12",
  "last_performance": "2026-01-04",
  "active_years": 17,
  "recent_performances_2years": 22
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
    "date": "28.02.2026",
    "iso_date": "2026-02-28",
    "year": 2026,
    "month": 2,
    "url": "https://www.berghain.berlin/de/event/79500/",
    "total_artists": 14
  }
]
```

#### `GET /api/shows/:eventId`

Event details with full lineup.

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
| `GET /api/flyers` | Monthly flyer archive metadata |

### Bulk Export

| Endpoint | Description |
| --- | --- |
| `GET /api/export/artists` | Export all artists as JSON |
| `GET /api/export/events` | Export all events as JSON |
| `GET /api/export/performances` | Export all performances as JSON |

> **Note**: Export endpoints cost $0.10 per request for AI crawlers via x402.

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
