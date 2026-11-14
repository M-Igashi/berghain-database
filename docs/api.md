# API Reference

**Base URL**: `https://berghain.ravers.workers.dev`

| | |
| --- | --- |
| **Access** | Free and open. No account, no API key, CORS enabled for every origin |
| **Format** | JSON over HTTPS, `GET` only |
| **Paid** | Only bulk exports ($0.10), via the [x402 protocol](x402-ai-paywall.md) |
| **License** | Data is [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/). See [Attribution](#attribution) |
| **Machine-readable** | [`/openapi.json`](https://berghain.ravers.workers.dev/openapi.json) (OpenAPI 3.1), [`/llms.txt`](https://berghain.ravers.workers.dev/llms.txt) |

**Contents**

- [Quick Start](#quick-start)
- [Endpoint Index](#endpoint-index)
- [Conventions](#conventions)
- [Integration Guide](#integration-guide)
- Endpoints: [Statistics](#statistics) · [Artists](#artists) · [Rankings](#rankings) · [Events](#events) · [Residents](#residents) · [Reference Data](#reference-data) · [Crawl Statistics](#crawl-statistics)
- [Paid Endpoints (x402)](#paid-endpoints-x402)
- [Discovery, Feeds, and Status](#discovery-feeds-and-status)
- [HTML Pages](#html-pages)
- [Recent Changes](#recent-changes)

## Quick Start

```bash
BASE="https://berghain.ravers.workers.dev"

# Who plays next, floor by floor
curl "$BASE/api/shows/upcoming"

# All-time top 20
curl "$BASE/api/artists/ranking?limit=20"

# Find an artist (accents optional)
curl "$BASE/api/artists?search=rodhad"

# Every night that artist played
curl "$BASE/api/artists/13/performances"
```

## Endpoint Index

All of these are free.

| Endpoint | Returns |
| --- | --- |
| [`GET /api/stats`](#get-apistats) | Totals and the split between the two floors |
| [`GET /api/stats/monthly`](#get-apistatsmonthly) | Events per calendar month, across all years |
| [`GET /api/artists`](#get-apiartists) | List or search artists |
| [`GET /api/artists/:id`](#get-apiartistsid) | One artist by id |
| [`GET /api/artists/by-name/:name`](#get-apiartistsby-namename) | One artist by exact name |
| [`GET /api/artists/:id/stats`](#get-apiartistsidstats) | Career statistics of one artist |
| [`GET /api/artists/:id/performances`](#get-apiartistsidperformances) | Every night one artist played |
| [`GET /api/performances?artist_ids=`](#get-apiperformances) | Histories of up to 200 artists in one request |
| [`GET /api/artists/ranking`](#get-apiartistsranking) | All-time ranking, optionally per floor |
| [`GET /api/artists/ranking/year/:year`](#get-apiartistsrankingyearyear) | Ranking for one year |
| [`GET /api/shows`](#get-apishows) | All nights, newest first |
| [`GET /api/shows/upcoming`](#get-apishowsupcoming) | Upcoming nights with the lineup of each floor |
| [`GET /api/residents/current`](#get-apiresidentscurrent) | Most active artists of the past 3 years, split by floor |
| [`GET /api/years`](#get-apiyears) | Years covered |
| [`GET /api/period`](#get-apiperiod) | First and last event date |
| [`GET /api/flyers`](#get-apiflyers) | Index of the 2004–2009 flyer archive, with links to the official PDFs |
| [`GET /api/public/crawl-summary`](#get-apipubliccrawl-summary) | Aggregated AI crawler statistics |

## Conventions

### Responses and errors

Successful responses are JSON with status `200`. Errors are JSON objects with a single `error` string.

| Status | Meaning |
| --- | --- |
| `200` | Success. Unknown years and ids inside a list request give an empty list, not an error |
| `304` | Unchanged since the `ETag` you sent in `If-None-Match` (empty body) |
| `400` | Invalid parameter, for example `month=13` or more than 200 `artist_ids` |
| `402` | Payment required (bulk exports only) |
| `404` | Artist not found |
| `500` | Server error |

```json
{ "error": "Artist not found" }
```

### Paging

List endpoints accept `limit`, plus either `offset` or `page`. **`limit=0` returns the complete list.** Defaults differ per endpoint and are listed in each parameter table.

### Caching

API responses carry an `ETag`. Send it back as `If-None-Match` and an unchanged response returns an empty `304`. Per-artist endpoints, yearly rankings, and upcoming lineups also send `Cache-Control: public, max-age=3600`.

### Names and search

Names follow the original billing, including `aka` aliases and collectives. See [Aliases & Collective Acts](aliases-and-units.md). Search is Unicode-aware: Rødhåd or Ø [Phase] match as written and by their ASCII spelling (`rodhad`).

### Ids and dates

| Field | Notes |
| --- | --- |
| Artist `id` | Stable. It only disappears when a duplicate entry is merged into another one |
| `event_id` | The official berghain.berlin event id, or a synthetic `YYYYMMDD` id for 2004–2009 flyer-era nights |
| `iso_date` | Always `YYYY-MM-DD`. Use it for sorting and parsing |
| `date` | Display string whose format varies between eras. Do not parse it |
| `venue` | Either `Berghain` or `Panorama Bar` |
| `url` | The original berghain.berlin listing, or the source flyer PDF for 2004–2009 |

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

`by-name` is an exact, case-sensitive match. If you are unsure of the spelling, use `/api/artists?search=` and pick the right result.

Each performance carries two links: `url` is the original berghain.berlin listing (or the source flyer PDF for 2004–2009), and `https://berghain.ravers.workers.dev/shows/{event_id}` is the full lineup of that night on this site.

### Sync many artists

```bash
# 1. Every artist in one response: match your names against it locally and store the ids
curl "https://berghain.ravers.workers.dev/api/artists?limit=0"

# 2. Histories for up to 200 artists per request, keyed by artist id
curl "https://berghain.ravers.workers.dev/api/performances?artist_ids=13,1416,2240"
```

Send your ids in chunks of up to 200. If you need every artist, the [performances export](#bulk-exports) returns everything in one file for $0.10.

An artist id only changes when a duplicate entry is merged into another one. Such an id comes back from step 2 as an empty list (or as 404 from the single-artist endpoints). Match that name again against the list from step 1.

### Keep it light

- **Refresh daily at most.** Lineups are imported in monthly batches, plus occasional corrections, so a daily rebuild is always current.
- **Send `If-None-Match`.** Unchanged responses come back as an empty `304`.
- **Need everything at once?** The [bulk exports](#bulk-exports) return all artists, events, or performances in a single file.

### Attribution

The data is licensed under [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/): free to use, share, and adapt, commercially or not, as long as you credit the source.

- Credit **Berghain Klubnacht Database** and link to `https://berghain.ravers.workers.dev`.
- When you show a specific artist, link to their page via the `page_url` field instead. Readers get the full history, yearly stats, and frequent co-performers. Use `page_url` as returned rather than building the URL yourself, so it always matches the current slug.

```html
<p>Source: <a href="https://berghain.ravers.workers.dev/artists/marcus-l">Berghain Klubnacht Database</a> (CC BY 4.0)</p>
```

### Need a different endpoint?

Several endpoints here exist because someone building on the data asked for them. One request shaped for your use case is easier for you and lighter on the database than many small ones, so please ask before you start scraping: [open a feature request](https://github.com/M-Igashi/berghain-database/issues/new?template=feature_request.md).

## Statistics

### `GET /api/stats`

Overall totals and the split between the two floors. Takes no parameters.

```json
{
  "total_artists": 2554,
  "total_events": 1069,
  "total_performances": 13785,
  "venue_breakdown": [
    { "venue": "Berghain", "count": 7080 },
    { "venue": "Panorama Bar", "count": 6705 }
  ]
}
```

### `GET /api/stats/monthly`

Events per calendar month, aggregated across all years (12 entries). Takes no parameters.

```json
[
  {
    "month": 1,
    "event_count": 95,
    "total_artists": 1110,
    "avg_artists_per_event": 11.68421052631579
  }
]
```

## Artists

Artist objects share these fields:

| Field | Description |
| --- | --- |
| `id` | Numeric artist id |
| `name` | Name as billed (may include `aka` aliases) |
| `total_performances` | Sets on both floors |
| `berghain_performances` | Sets on the Berghain main floor |
| `panorama_performances` | Sets at Panorama Bar |
| `page_url` | The artist's page on this site |

### `GET /api/artists`

List or search artists, ordered by total performances.

| Parameter | Type | Default | Description |
| --- | --- | --- | --- |
| `search` | string | none | Search term, matched against names and their ASCII spelling |
| `limit` | integer | 20 | Results per page. `limit=0` returns every artist (about 450 KB before compression) |
| `page` | integer | 1 | Page number |

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

Search results are paged too: a broad term returns the first 20 matches unless you raise `limit`.

### `GET /api/artists/:id`

A single artist by numeric id. Returns `404` if the id does not exist.

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

A single artist by exact, case-sensitive name (URL-encoded). Same response as `/api/artists/:id`, and `404` if there is no such name.

### `GET /api/artists/:id/stats`

Career statistics for an artist.

```json
{
  "name": "Ben Klock",
  "total_performances": 204,
  "berghain_performances": 193,
  "panorama_performances": 11,
  "first_performance": "2005-01-15",
  "last_performance": "2026-12-19",
  "active_years": 22,
  "recent_performances_2years": 13
}
```

`last_performance` can lie in the future, because announced lineups are imported ahead of time.

### `GET /api/artists/:id/performances`

Every performance of an artist, newest first.

| Parameter | Type | Default | Description |
| --- | --- | --- | --- |
| `limit` | integer | all | Return only the latest N performances |

```json
[
  {
    "event_id": 80747,
    "title": "Klubnacht",
    "date": "Saturday 17.10.2026 start 23:59",
    "iso_date": "2026-10-17",
    "url": "https://www.berghain.berlin/en/event/80747/",
    "venue": "Berghain",
    "artist_name": "Rødhåd"
  }
]
```

### `GET /api/performances`

Performance histories for several artists in one request.

| Parameter | Type | Default | Description |
| --- | --- | --- | --- |
| `artist_ids` | string | required | Comma-separated artist ids, **at most 200** |

```bash
curl "https://berghain.ravers.workers.dev/api/performances?artist_ids=13,1416"
```

The response is an object keyed by artist id. Each value is the same list that `/api/artists/:id/performances` returns for that artist, newest first. Ids that do not exist (for example after a duplicate entry was merged) map to an empty list. A missing or non-numeric `artist_ids`, or more than 200 ids, returns `400`.

```json
{
  "13": [
    {
      "event_id": 80747,
      "title": "Klubnacht",
      "date": "Saturday 17.10.2026 start 23:59",
      "iso_date": "2026-10-17",
      "url": "https://www.berghain.berlin/en/event/80747/",
      "venue": "Berghain",
      "artist_name": "Rødhåd"
    }
  ],
  "1416": [
    {
      "event_id": 66512,
      "title": "Klubnacht",
      "date": "22.02.2020",
      "iso_date": "2020-02-22",
      "url": "https://www.berghain.berlin/de/event/66512/",
      "venue": "Berghain",
      "artist_name": "Marcus L"
    }
  ]
}
```

## Rankings

### `GET /api/artists/ranking`

All-time ranking by number of sets.

| Parameter | Type | Default | Description |
| --- | --- | --- | --- |
| `venue` | string | none | `berghain` or `panorama` to rank by that floor only |
| `limit` | integer | all | Number of results |
| `offset` | integer | 0 | Skip the first N results (needs `limit`) |

```bash
curl "https://berghain.ravers.workers.dev/api/artists/ranking?venue=panorama&limit=1"
```

```json
[
  {
    "id": 25,
    "name": "nd_baumecker",
    "total_performances": 197,
    "berghain_performances": 40,
    "panorama_performances": 157,
    "page_url": "https://berghain.ravers.workers.dev/artists/nd-baumecker"
  }
]
```

### `GET /api/artists/ranking/year/:year`

Ranking for a single year. Same parameters and response as above, except that `limit` defaults to 50 and the counts cover only that year. A year outside [`/api/years`](#get-apiyears) returns an empty list.

## Events

### `GET /api/shows`

All nights, newest first. Announced future nights are included.

| Parameter | Type | Default | Description |
| --- | --- | --- | --- |
| `year` | integer | none | Filter by year. A year without data returns an empty list |
| `month` | integer | none | Filter by month (1–12). Anything else returns `400` |
| `limit` | integer | all | Number of results |
| `offset` | integer | 0 | Skip the first N results (needs `limit`) |

```json
[
  {
    "id": 1239,
    "event_id": 80757,
    "title": "Holy Klubnacht",
    "date": "Saturday 26.12.2026 start 23:59",
    "iso_date": "2026-12-26",
    "year": 2026,
    "month": 12,
    "url": "https://www.berghain.berlin/en/event/80757/",
    "total_artists": 7,
    "actual_performances": 7
  }
]
```

For 2004–2009 events the `url` points to the source flyer PDF (for example `https://www.berghain.berlin/documents/110/berghain-flyer-2005-12.pdf`) rather than an event page.

There is no JSON endpoint for a single past night. Its lineup is available as an HTML or Markdown page at `/shows/:event_id`.

### `GET /api/shows/upcoming`

Nights that have not ended yet, soonest first, with the announced lineup of each floor. Takes no parameters.

```json
[
  {
    "event_id": 80751,
    "title": "FC Snax United + Klubnacht",
    "date": "Saturday 14.11.2026 start 23:59",
    "iso_date": "2026-11-14",
    "url": "https://www.berghain.berlin/en/event/80751/",
    "berghain": ["Beste Hira", "Efdemin"],
    "panorama_bar": ["Alexander Skancke", "Alinka"],
    "page_url": "https://berghain.ravers.workers.dev/shows/80751"
  }
]
```

Lineups are imported about once a month from berghain.berlin. Late changes and set times (the running order) are not tracked.

## Residents

### `GET /api/residents/current`

Berghain does not publish a resident list, so this is a data-driven one: the 30 most active artists of the past 3 years (plus everyone tied with 30th place), limited to artists with at least 2 sets in the last 12 months. Takes no parameters.

```json
[
  {
    "id": 25,
    "name": "nd_baumecker",
    "total_performances_3y": 25,
    "berghain_performances_3y": 0,
    "panorama_performances_3y": 25,
    "recent_performances_1y": 10,
    "last_performance": "2026-11-21",
    "total_performances": 197,
    "berghain_performances": 40,
    "panorama_performances": 157
  }
]
```

| Field | Description |
| --- | --- |
| `total_performances_3y` | Sets in the past 3 years (the ranking key) |
| `berghain_performances_3y`, `panorama_performances_3y` | The same 3-year count, split by floor |
| `recent_performances_1y` | Sets in the last 12 months |
| `last_performance` | Most recent date, which can be an announced future night |
| `total_performances`, `berghain_performances`, `panorama_performances` | All-time counts |

## Reference Data

### `GET /api/years`

Years covered by the database, newest first.

```json
[2026, 2025, 2024]
```

### `GET /api/period`

First and last event date.

```json
{ "start_date": "2004-12-18", "end_date": "2026-12-26" }
```

### `GET /api/flyers`

Index of the 61 monthly flyers from 2004–2009. The flyers are Berghain's own work, so this site does not host them: `source_url` is the official PDF on berghain.berlin, and `/flyers/:filename` redirects there.

```json
[
  {
    "filename": "berghain-flyer-2009-12.pdf",
    "year": 2009,
    "month": 12,
    "display_name": "2009-12",
    "url": "/flyers/berghain-flyer-2009-12.pdf",
    "full_url": "https://berghain.ravers.workers.dev/flyers/berghain-flyer-2009-12.pdf",
    "source_url": "https://www.berghain.berlin/documents/63/berghain-flyer-2009-12.pdf"
  }
]
```

## Crawl Statistics

### `GET /api/public/crawl-summary`

Aggregated AI crawler activity, the data behind the [public dashboard](https://berghain.ravers.workers.dev/dashboard/crawl-stats). Paths are normalized to patterns such as `/artists/*`.

| Parameter | Type | Default | Description |
| --- | --- | --- | --- |
| `days` | integer | 30 | Period to cover, 1–90 |

```json
{
  "generated_at": "2026-11-14T10:35:28.055Z",
  "period_days": 7,
  "total_requests": 6804,
  "total_paid_requests": 0,
  "total_revenue_usd": "$0.00",
  "by_crawler": [{ "name": "Meta-ExternalAgent", "requests": 4889, "paid_requests": 0, "revenue_usd": "$0.00" }],
  "top_paths": [{ "path": "/shows/*", "requests": 395 }],
  "daily_totals": [{ "date": "2026-11-14", "requests": 383, "paid_requests": 0, "revenue_cents": 0 }]
}
```

## Paid Endpoints (x402)

These routes answer `402 Payment Required` to every caller until paid through [x402](x402-ai-paywall.md) in USDC on Solana or Base. Everything else in this document is free.

### Bulk exports

Full-table dumps in JSON (default) or CSV (`?format=csv`). **$0.10 per request.**

| Endpoint | Description |
| --- | --- |
| `GET /api/export/artists` | All artists |
| `GET /api/export/events` | All events |
| `GET /api/export/performances` | All performances |

## Discovery, Feeds, and Status

| Endpoint | Description |
| --- | --- |
| `GET /openapi.json` | OpenAPI 3.1 specification, with x402 price annotations |
| `GET /llms.txt` | LLM discovery file (summary) |
| `GET /llms-full.txt` | LLM discovery file (full API docs) |
| `GET /.well-known/x402` | x402 payment manifest (routes, pricing, wallet) |
| `GET /.well-known/agentic-capabilities.json` | Agent capability descriptor |
| `GET /sitemap.xml` | XML sitemap |
| `GET /feed.xml` | RSS feed of recent events |
| `GET /robots.txt` | Robots directives |
| `GET /health` | Health check |

## HTML Pages

| Path | Description |
| --- | --- |
| `/` | Upcoming lineups, current residents, rankings, and the event archive |
| `/current-residents` | Current residents with the method behind the list |
| `/artists/:slug` | Artist profile (the `page_url` of an artist) |
| `/shows/:event_id` | Lineup of a single night, both floors |
| `/dashboard/crawl-stats` | AI crawler statistics dashboard |

Every page except the dashboard returns structured Markdown when requested with `Accept: text/markdown`:

```bash
curl -H "Accept: text/markdown" https://berghain.ravers.workers.dev/artists/ben-klock
```

## Recent Changes

| When | Change |
| --- | --- |
| 14 November 2026 | `/api/performances` accepts up to 200 `artist_ids` per request again, free. If your sync got `400` or `402` for more than 20 ids between 12 and 14 November, it works again without changes |
| November 2026 | Flyer PDFs are no longer sold: `/flyers/*` redirects to the official PDF, and `/api/flyers` adds `source_url` |
| November 2026 | `/api/residents/current` adds `berghain_performances_3y` and `panorama_performances_3y` |
| November 2026 | New `/api/shows/upcoming` |
| November 2026 | Ranking endpoints return `page_url` |
| October 2026 | New `/api/performances` batch endpoint |
