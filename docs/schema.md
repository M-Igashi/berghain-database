# Database Schema

The database uses Cloudflare D1 (SQLite) with three core tables.

## Entity Relationship

```
┌─────────────────┐       ┌──────────────────┐       ┌─────────────────┐
│     artists     │       │   performances   │       │     events      │
├─────────────────┤       ├──────────────────┤       ├─────────────────┤
│ id (PK)         │◄──────│ artist_id (FK)   │       │ id (PK)         │
│ name            │       │ event_id (FK)    │──────►│ event_id        │
│ normalized_name │       │ venue            │       │ title           │
│ total_perfs     │       └──────────────────┘       │ date / iso_date │
│ berghain_perfs  │                                  │ year / month    │
│ panorama_perfs  │                                  │ url             │
└─────────────────┘                                  │ total_artists   │
                                                     └─────────────────┘
```

## Tables

### `artists`

| Column | Type | Description |
| --- | --- | --- |
| `id` | INTEGER | Primary key (auto-increment) |
| `name` | TEXT | Unique artist/DJ name |
| `normalized_name` | TEXT | Lowercase, hyphen-separated (used for URL slugs and search) |
| `total_performances` | INTEGER | Total appearance count across all venues |
| `berghain_performances` | INTEGER | Appearances on the Berghain (main) floor |
| `panorama_performances` | INTEGER | Appearances at Panorama Bar |

### `events`

| Column | Type | Description |
| --- | --- | --- |
| `id` | INTEGER | Primary key (auto-increment) |
| `event_id` | INTEGER | Original Berghain event ID (unique) |
| `title` | TEXT | Event title (typically "Klubnacht") |
| `date` | TEXT | Human-readable display string, e.g. `Saturday 28.02.2026 start 00:00` |
| `iso_date` | TEXT | ISO 8601 format: `YYYY-MM-DD` |
| `year` | INTEGER | Event year |
| `month` | INTEGER | Event month (1–12) |
| `url` | TEXT | Source URL: a berghain.berlin event page (2009–present) or the source flyer PDF (2004–2009) |
| `total_artists` | INTEGER | Billed act count from the source (kept stable; may differ from the joined performance count) |

> **`event_id`**: for 2009-present events this is the original berghain.berlin event ID; for 2004–2009 flyer-sourced events it is a synthetic ID (e.g. `20051217`) since no online event page exists.

### `performances`

| Column | Type | Description |
| --- | --- | --- |
| `id` | INTEGER | Primary key (auto-increment) |
| `event_id` | INTEGER | Foreign key → `events(id)` |
| `artist_id` | INTEGER | Foreign key → `artists(id)` |
| `venue` | TEXT | Either `"Berghain"` or `"Panorama Bar"` |

## Indexes

- `artists.name` — UNIQUE
- `artists.normalized_name` — for slug-based lookups
- `events.event_id` — UNIQUE
- `events.iso_date` — for date range queries
- `performances(event_id, artist_id)` — composite for join performance
