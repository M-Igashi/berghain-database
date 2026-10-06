# API Reference

**Base URL**: `https://berghain.ravers.workers.dev`

- **Free and open**: no accounts, no API keys, CORS enabled. Every endpoint below is free for everyone, including AI crawlers. Only bulk exports and flyer PDFs are paid, via the [x402 protocol](x402-ai-paywall.md).
- **Unicode-aware search**: names like Rødhåd or Ø [Phase] match as written and by their ASCII spelling (`rodhad`).
- **Conditional requests**: API responses carry an `ETag`. Send it back as `If-None-Match` and an unchanged response returns an empty `304`.
- **Content negotiation**: HTML pages return Markdown when requested with `Accept: text/markdown`.
- **License**: the data is [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/). See [Attribution](#attribution).

**Contents**: [Integration Guide](#integration-guide) · [Statistics](#statistics) · [Artists](#artists) · [Rankings](#rankings) · [Events](#events) · [Residents](#residents) · [Other Endpoints](#other-endpoints) · [Bulk Export](#bulk-export-paid-via-x402) · [HTML Pages](#html-pages)

## Integration Guide

### Show an artist's Berghain history

Two requests per artist: resolve the name to an artist, then fetch their nights.

```bash
# 1. Exact name → id, performance counts, page_url
curl "https://berghain.ravers.workers.dev/api/artists/by-name/Marcus%20L"

# 2. Every night the artist played, newest first
curl "https://berghain.ravers.workers.dev/api/artists/1416/performances"
```

```javascript
const BASE = "https://berghain.ravers.workers.dev";

async function berghainHistory(name) {
  const res = await fetch(`${BASE}/api/artists/by-name/${encodeURIComponent(name)}`);
  if (res.status === 404) return null;
  const artist = await res.json();
  const nights = await fetch(`${BASE}/api/artists/${artist.id}/performances`).then((r) => r.json());
  return { ...artist, nights };
}
```

`by-name` is an exact, case-sensitive match. If you are unsure of the spelling, use `/api/artists?search=` and pick the right result. Names follow the original billing, including `aka` aliases and collectives; see [Aliases & Collective Acts](aliases-and-units.md).

Each performance carries two links: `url` is the original berghain.berlin listing (or the source flyer PDF for 2004–2009), and `https://berghain.ravers.workers.dev/shows/{event_id}` is the full lineup of that night on this site.

### Keep it light

- **Match many artists in one request.** `GET /api/artists?limit=5000` returns every artist (about 270 KB). Match your names against it locally instead of looking each one up.
- **Store artist ids.** An id only changes when a duplicate entry is merged into another one. If a stored id returns 404, resolve the name again.
- **Refresh daily at most.** Lineups are imported in monthly batches, plus occasional corrections, so a daily rebuild is always current.
- **Send `If-None-Match`.** Unchanged responses come back as an empty `304`.
- **Need everything at once?** The [bulk exports](#bulk-export-paid-via-x402) return all artists, events or performances in a single file.

### Attribution

The data is licensed under [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/): free to use, share and adapt, commercially or not, as long as you credit the source.

- Credit **Berghain Klubnacht Database** and link to `https://berghain.ravers.workers.dev`.
- When you show a specific artist, link to their page via the `page_url` field instead. Readers get the full history, yearly stats and frequent co-performers. Use `page_url` as returned rather than building the URL yourself, so it always matches the current slug.

```html
<p>Source: <a href="https://berghain.ravers.workers.dev/artists/marcus-l">Berghain Klubnacht Database</a> (CC BY 4.0)</p>
```

### Missing something?

If a different endpoint would make your integration simpler, for example fetching several artists in one request, [open a feature request](https://github.com/M-Igashi/berghain-database/issues/new?template=feature_request.md).

## Statistics

### `GET /api/stats`

Overall totals and the split between the two floors.

```json
{
  "total_artists": 2553,
  "total_events": 1061,
  "total_performances": 13711,
  "venue_breakdown": [
    { "venue": "Berghain", "count": 7022 },
    { "venue": "Panorama Bar", "count": 6689 }
  ]
}
```

### `GET /api/stats/monthly`

Events per calendar month, aggregated across all years. Takes no parameters.

```json
[
  {
    "month": 1,
    "event_count": 95,
    "total_artists": 1110,
    "avg_artists_per_event": 11.68
  }
]
```

## Artists

Artist objects share these fields:

| Field | Description |
| --- | --- |
| `id` | Numeric artist id |
| `name` | Name as billed (may include `aka` aliases) |
| `total_performances` | Performances on both floors |
| `berghain_performances` | Performances on the Berghain main floor |
| `panorama_performances` | Performances at Panorama Bar |
| `page_url` | The artist's page on this site. Returned by `/api/artists`, `/api/artists/:id` and `/api/artists/by-name/:name` |

### `GET /api/artists`

List or search artists, ordered by total performances.

| Parameter | Type | Default | Description |
| --- | --- | --- | --- |
| `search` | string | none | Search term, matched against names and their ASCII spelling |
| `page` | integer | 1 | Page number |
| `limit` | integer | 20 | Results per page. `limit=5000` returns every artist |

```bash
curl "https://berghain.ravers.workers.dev/api/artists?search=rødhåd"
```

```json
[
  {
    "id": 13,
    "name": "Rødhåd",
    "total_performances": 68,
    "berghain_performances": 67,
    "panorama_performances": 1,
    "page_url": "https://berghain.ravers.workers.dev/artists/rodhad"
  }
]
```

### `GET /api/artists/:id`

A single artist by numeric id. Returns 404 if the id does not exist.

```json
{
  "id": 1416,
  "name": "Marcus L",
  "total_performances": 3,
  "berghain_performances": 2,
  "panorama_performances": 1,
  "page_url": "https://berghain.ravers.workers.dev/artists/marcus-l"
}
```

### `GET /api/artists/by-name/:name`

A single artist by exact, case-sensitive name (URL-encoded). Same response as `/api/artists/:id`.

### `GET /api/artists/:id/stats`

Career statistics for an artist.

```json
{
  "name": "Ben Klock",
  "total_performances": 204,
  "berghain_performances": 192,
  "panorama_performances": 12,
  "first_performance": "2005-01-15",
  "last_performance": "2026-10-10",
  "active_years": 22,
  "recent_performances_2years": 25
}
```

### `GET /api/artists/:id/performances`

Every performance of an artist, newest first.

| Parameter | Type | Default | Description |
| --- | --- | --- | --- |
| `limit` | integer | all | Return only the latest N performances |

```json
[
  {
    "event_id": 80746,
    "title": "Klubnacht",
    "date": "Saturday 10.10.2026 start 23:59",
    "iso_date": "2026-10-10",
    "url": "https://www.berghain.berlin/en/event/80746/",
    "venue": "Berghain",
    "artist_name": "Ben Klock"
  }
]
```

`event_id` is the official berghain.berlin id, or a synthetic `YYYYMMDD` id for 2004–2009 flyer-era nights. `venue` is either `Berghain` or `Panorama Bar`.

## Rankings

### `GET /api/artists/ranking`

All-time ranking by performance count.

| Parameter | Type | Default | Description |
| --- | --- | --- | --- |
| `limit` | integer | all | Number of results |
| `offset` | integer | 0 | Skip the first N results |
| `venue` | string | none | `berghain` or `panorama` to rank by that floor only |

```json
[
  {
    "id": 16,
    "name": "Ben Klock",
    "total_performances": 204,
    "berghain_performances": 192,
    "panorama_performances": 12
  }
]
```

### `GET /api/artists/ranking/year/:year`

Ranking for a single year. Same parameters and response as above, except that `limit` defaults to 50 and the counts cover only that year.

## Events

### `GET /api/shows`

All nights, newest first.

| Parameter | Type | Default | Description |
| --- | --- | --- | --- |
| `year` | integer | none | Filter by year |
| `month` | integer | none | Filter by month (1–12) |
| `limit` | integer | all | Number of results |
| `offset` | integer | 0 | Skip the first N results |

```json
[
  {
    "id": 1231,
    "event_id": 80749,
    "title": "Klubnacht",
    "date": "Saturday 31.10.2026 start 23:59",
    "iso_date": "2026-10-31",
    "year": 2026,
    "month": 10,
    "url": "https://www.berghain.berlin/en/event/80749/",
    "total_artists": 15,
    "actual_performances": 15
  }
]
```

For 2004–2009 events the `url` points to the source flyer PDF (e.g. `https://www.berghain.berlin/documents/110/berghain-flyer-2005-12.pdf`) rather than an event page. There is no JSON endpoint for a single night; its lineup is available as an HTML or Markdown page at `/shows/:event_id`.

## Residents

### `GET /api/residents/current`

Current residents, based on a 3-year rolling window with at least 2 shows in the last year.

```json
[
  {
    "id": 36,
    "name": "Steffi",
    "total_performances_3y": 29,
    "recent_performances_1y": 10,
    "last_performance": "2026-09-05",
    "total_performances": 159,
    "berghain_performances": 64,
    "panorama_performances": 95
  }
]
```

## Other Endpoints

| Endpoint | Description |
| --- | --- |
| `GET /api/years` | Years covered by the database |
| `GET /api/period` | First and last event date |
| `GET /api/flyers` | Index of the 2004–2009 flyer archive (free; the PDFs themselves are paid) |

### Machine-Readable Discovery

| Endpoint | Description |
| --- | --- |
| `GET /openapi.json` | OpenAPI 3 specification (with x402 price annotations) |
| `GET /.well-known/x402` | x402 payment manifest (routes, pricing, wallet) |
| `GET /.well-known/agentic-capabilities.json` | Agent capability descriptor |
| `GET /llms.txt` | LLM discovery file (summary) |
| `GET /llms-full.txt` | LLM discovery file (full API docs) |

### Feeds and Status

| Endpoint | Description |
| --- | --- |
| `GET /sitemap.xml` | XML sitemap for search engines |
| `GET /feed.xml` | RSS feed of recent events |
| `GET /robots.txt` | Robots directives |
| `GET /health` | Health check |

### Public Crawl Statistics

| Endpoint | Description |
| --- | --- |
| `GET /api/public/crawl-summary?days=N` | Aggregated AI crawler statistics (max 90 days) |
| `GET /dashboard/crawl-stats` | Visual crawl stats dashboard |

## Bulk Export (paid via x402)

Full-table dumps in JSON (default) or CSV (`?format=csv`). **$0.10 per request for everyone** via [x402](x402-ai-paywall.md).

| Endpoint | Description |
| --- | --- |
| `GET /api/export/artists` | All artists |
| `GET /api/export/events` | All events |
| `GET /api/export/performances` | All performances |

### Historical Flyers (2004–2009)

| Endpoint | Description |
| --- | --- |
| `GET /flyers/berghain-flyer-YYYY-MM.pdf` | Original monthly flyer PDF, **$0.01 per request** via x402 |

## HTML Pages

| Path | Description |
| --- | --- |
| `/` | Home page with overview statistics |
| `/current-residents` | Current resident DJs |
| `/artists/:slug` | Artist profile (the `page_url` of an artist) |
| `/shows/:event_id` | Lineup of a single night |

All HTML pages return structured Markdown with `Accept: text/markdown`.
