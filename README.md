<p align="center">
  <img src="https://berghain.ravers.workers.dev/favicon.png" alt="Berghain Database Logo" width="120">
</p>

<h1 align="center">🎵 Berghain Klubnacht Database</h1>

<p align="center">
  <strong>The most comprehensive historical archive of DJ performances at Berlin's legendary Berghain nightclub</strong>
</p>

<p align="center">
  <a href="https://berghain.ravers.workers.dev">
    <img src="https://img.shields.io/badge/🌐_Live_Demo-berghain.ravers.workers.dev-black?style=for-the-badge" alt="Live Demo">
  </a>
</p>

<p align="center">
  <a href="https://berghain.ravers.workers.dev/health">
    <img src="https://img.shields.io/badge/API_Status-Live-00d26a?style=flat-square&logo=cloudflare" alt="API Status">
  </a>
  <a href="#-api-documentation">
    <img src="https://img.shields.io/badge/API-RESTful-blue?style=flat-square" alt="REST API">
  </a>
  <img src="https://img.shields.io/badge/Response_Time-<100ms-success?style=flat-square" alt="Response Time">
  <img src="https://img.shields.io/badge/Uptime-99.9%25-brightgreen?style=flat-square" alt="Uptime">
  <a href="LICENSE">
    <img src="https://img.shields.io/badge/License-MIT-yellow?style=flat-square" alt="License">
  </a>
</p>

<p align="center">
  <a href="#-quick-start">Quick Start</a> •
  <a href="#-api-documentation">API Docs</a> •
  <a href="#-database-schema">Schema</a> •
  <a href="#-architecture">Architecture</a> •
  <a href="#-support-this-project">Support</a>
</p>

---

## 📊 Database at a Glance

<table>
<tr>
<td align="center"><h3>2,099+</h3><sub>Artists</sub></td>
<td align="center"><h3>748+</h3><sub>Events</sub></td>
<td align="center"><h3>10,466+</h3><sub>Performances</sub></td>
<td align="center"><h3>16+</h3><sub>Years</sub></td>
</tr>
</table>

> **Coverage Period**: November 2009 - February 2026 (ongoing updates)

This project preserves the history of one of the world's most influential Techno institutions by cataloging every DJ performance at Berghain and Panorama Bar. Updated monthly with the latest Klubnacht lineups.

---

## 🚀 Quick Start

```bash
# Get database statistics
curl https://berghain.ravers.workers.dev/api/stats

# Search for an artist
curl "https://berghain.ravers.workers.dev/api/artists?search=ben+klock"

# Get top 10 performers
curl "https://berghain.ravers.workers.dev/api/artists/ranking?limit=10"

# Get current residents
curl https://berghain.ravers.workers.dev/api/residents/current
```

---

## 📚 API Documentation

**Base URL**: `https://berghain.ravers.workers.dev`

### Core Statistics

<details>
<summary><code>GET /api/stats</code> — Database Statistics</summary>

Returns overall database statistics including total counts and venue breakdown.

**Response:**
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
</details>

<details>
<summary><code>GET /api/period</code> — Data Coverage Period</summary>

**Response:**
```json
{
  "start_date": "2009-11-07",
  "end_date": "2026-02-28"
}
```
</details>

<details>
<summary><code>GET /api/years</code> — Available Years</summary>

**Response:**
```json
[2026, 2025, 2024, 2023, 2022, 2021, 2020, 2019, 2018, 2017, 2016, 2015, 2014, 2013, 2012, 2011, 2010, 2009]
```
</details>

### Artist Endpoints

<details>
<summary><code>GET /api/artists/ranking</code> — Artist Rankings</summary>

Returns artists ranked by total performance count.

**Query Parameters:**
| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `limit` | integer | 50 | Number of results (max 500) |

**Response:**
```json
[
  {
    "id": 14,
    "name": "Norman Nodge",
    "total_performances": 149,
    "berghain_performances": 142,
    "panorama_performances": 7
  },
  {
    "id": 16,
    "name": "Ben Klock",
    "total_performances": 138,
    "berghain_performances": 130,
    "panorama_performances": 8
  }
]
```
</details>

<details>
<summary><code>GET /api/artists/ranking/year/:year</code> — Yearly Rankings</summary>

Returns artist rankings for a specific year.

**Example:** `/api/artists/ranking/year/2024`

**Response:** Same format as `/api/artists/ranking`
</details>

<details>
<summary><code>GET /api/artists</code> — Search & Browse Artists</summary>

Search and browse all artists with pagination.

**Query Parameters:**
| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `search` | string | — | Search term (supports special chars: ¥, Ø, ø, À-ÿ) |
| `page` | integer | 1 | Page number |
| `limit` | integer | 20 | Results per page |

**Example:**
```bash
curl "https://berghain.ravers.workers.dev/api/artists?search=rødhåd"
```
</details>

<details>
<summary><code>GET /api/artists/:artistId</code> — Artist by ID</summary>

Get basic artist information by database ID.

**Response:**
```json
{
  "id": 16,
  "name": "Ben Klock",
  "total_performances": 138,
  "berghain_performances": 130,
  "panorama_performances": 8
}
```
</details>

<details>
<summary><code>GET /api/artists/by-name/:artistName</code> — Artist by Name</summary>

Find artist by exact name match (URL-encoded).

**Example:** `/api/artists/by-name/Ben%20Klock`
</details>

<details>
<summary><code>GET /api/artists/:artistId/stats</code> — Artist Statistics</summary>

Get detailed statistics for an artist including career span.

**Response:**
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
</details>

<details>
<summary><code>GET /api/artists/:artistId/performances</code> — Performance History</summary>

Get all performances for a specific artist.

**Query Parameters:**
| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `limit` | integer | — | Maximum results to return |

**Response:**
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
</details>

### Event Endpoints

<details>
<summary><code>GET /api/shows</code> — Browse Events</summary>

Browse all Klubnacht events with filtering.

**Query Parameters:**
| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `limit` | integer | 20 | Number of results |

**Response:**
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
    "total_artists": 14,
    "actual_performances": 14
  }
]
```
</details>

### Resident Analysis

<details>
<summary><code>GET /api/residents/current</code> — Current Residents</summary>

Returns current active residents based on 3-year rolling performance analysis.

**Response:**
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
</details>

### Analytics

<details>
<summary><code>GET /api/stats/monthly</code> — Monthly Statistics</summary>

Get monthly performance statistics aggregated by year.

**Query Parameters:**
| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `year` | integer | current | Specific year to analyze |

**Response:**
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
</details>

### Feeds & SEO

| Endpoint | Description |
|----------|-------------|
| `GET /sitemap.xml` | XML sitemap for search engines |
| `GET /feed.xml` | RSS feed of recent events |
| `GET /robots.txt` | Robots exclusion protocol |
| `GET /health` | API health check endpoint |

---

## 🗄️ Database Schema

The database uses SQLite (Cloudflare D1) with three core tables:

### `artists`
| Column | Type | Description |
|--------|------|-------------|
| `id` | INTEGER | Primary key (auto-increment) |
| `name` | TEXT | Unique artist name |
| `normalized_name` | TEXT | Lowercase, hyphen-separated for search |
| `total_performances` | INTEGER | Total appearance count |
| `berghain_performances` | INTEGER | Main floor appearances |
| `panorama_performances` | INTEGER | Panorama Bar appearances |

### `events`
| Column | Type | Description |
|--------|------|-------------|
| `id` | INTEGER | Primary key (auto-increment) |
| `event_id` | INTEGER | Original Berghain event ID (unique) |
| `title` | TEXT | Event title (usually "Klubnacht") |
| `date` | TEXT | Display format (DD.MM.YYYY) |
| `iso_date` | TEXT | ISO format (YYYY-MM-DD) |
| `year` | INTEGER | Event year |
| `month` | INTEGER | Event month |
| `url` | TEXT | Original event URL |
| `total_artists` | INTEGER | Number of artists |

### `performances`
| Column | Type | Description |
|--------|------|-------------|
| `id` | INTEGER | Primary key (auto-increment) |
| `event_id` | INTEGER | References `events(id)` |
| `artist_id` | INTEGER | References `artists(id)` |
| `venue` | TEXT | "Berghain" or "Panorama Bar" |

### Entity Relationship

```
┌─────────────┐       ┌──────────────────┐       ┌─────────────┐
│   artists   │       │   performances   │       │   events    │
├─────────────┤       ├──────────────────┤       ├─────────────┤
│ id (PK)     │◄──────│ artist_id (FK)   │       │ id (PK)     │
│ name        │       │ event_id (FK)    │──────►│ event_id    │
│ normalized  │       │ venue            │       │ title       │
│ total_perf  │       │                  │       │ date        │
│ berghain    │       └──────────────────┘       │ iso_date    │
│ panorama    │                                  │ year/month  │
└─────────────┘                                  │ url         │
                                                 └─────────────┘
```

---

## 🏗️ Architecture

Built on Cloudflare's global edge network for maximum performance and reliability.

### Tech Stack

```
┌─────────────────────────────────────────────────────────────────┐
│                         Client Request                          │
└─────────────────────────────────────────────────────────────────┘
                                │
                                ▼
┌─────────────────────────────────────────────────────────────────┐
│                    Cloudflare Edge Network                       │
│                      (300+ global PoPs)                          │
└─────────────────────────────────────────────────────────────────┘
                                │
                                ▼
┌─────────────────────────────────────────────────────────────────┐
│                      Workers Runtime                             │
│  ┌─────────────────────────────────────────────────────────┐    │
│  │                   Hono Framework                         │    │
│  │              (TypeScript, Edge-native)                   │    │
│  └─────────────────────────────────────────────────────────┘    │
│                                │                                 │
│                  ┌─────────────┴─────────────┐                  │
│                  ▼                           ▼                  │
│  ┌───────────────────────────┐  ┌───────────────────────────┐  │
│  │   3-Tier Cache System     │  │      R2 Storage           │  │
│  │ ┌───────────────────────┐ │  │  (Static Assets)          │  │
│  │ │  In-Memory (L1)       │ │  └───────────────────────────┘  │
│  │ │  - Per-instance       │ │                                 │
│  │ │  - 2-24h TTL          │ │                                 │
│  │ └───────────────────────┘ │                                 │
│  │ ┌───────────────────────┐ │                                 │
│  │ │  KV Storage (L2)      │ │                                 │
│  │ │  - Global, persistent │ │                                 │
│  │ │  - 7-30 day TTL       │ │                                 │
│  │ └───────────────────────┘ │                                 │
│  │ ┌───────────────────────┐ │                                 │
│  │ │  D1 Database (L3)     │ │                                 │
│  │ │  - Source of truth    │ │                                 │
│  │ │  - SQLite at edge     │ │                                 │
│  │ └───────────────────────┘ │                                 │
│  └───────────────────────────┘                                 │
└─────────────────────────────────────────────────────────────────┘
```

### 3-Tier Cache System

The API implements an advanced caching strategy that reduces D1 database reads by **~95%**:

| Layer | Storage | Scope | TTL | Use Case |
|-------|---------|-------|-----|----------|
| **L1** | In-Memory Map | Per-instance | 2-24h | Ultra-fast repeated requests |
| **L2** | Workers KV | Global | 7-30 days | Cross-instance cache sharing |
| **L3** | D1 Database | Global | Permanent | Source of truth |

**Cache Flow:**
```
Request → L1 Hit? → Return
              ↓ Miss
         L2 Hit? → Populate L1 → Return
              ↓ Miss
         D1 Query → Populate L1 + L2 → Return
```

### Performance Characteristics

| Metric | Value |
|--------|-------|
| **Average Response Time** | <100ms |
| **Cache Hit Rate** | >95% |
| **Global Availability** | 99.9%+ |
| **Edge Locations** | 300+ cities |

### Key Technologies

| Component | Technology | Purpose |
|-----------|------------|---------|
| Runtime | Cloudflare Workers | Serverless edge computing |
| Framework | Hono | Lightweight, TypeScript-first |
| Database | Cloudflare D1 | Edge SQLite database |
| Cache | Workers KV | Global key-value storage |
| Storage | Cloudflare R2 | Static asset hosting |
| Language | TypeScript | Type-safe development |

---

## 🔧 Technical Features

### Search Capabilities
- **Normalized search**: Handles special characters (¥, Ø, ø, À-ÿ, etc.)
- **Case-insensitive**: "Ben Klock" = "ben klock"
- **Partial matching**: Search substrings of artist names

### API Features
- **CORS enabled**: Works from any origin
- **Rate limiting**: Generous fair-use policy
- **Pagination**: Standard `page` and `limit` parameters
- **ISO dates**: Machine-readable date formats

### SEO & Discoverability
- **Dynamic sitemap**: Updated automatically with new events
- **RSS feed**: Subscribe to latest Klubnacht lineups
- **IndexNow integration**: Instant search engine indexing
- **Structured data**: JSON-LD schema markup

---

## 📈 Use Cases

### For Developers
- Build Techno music applications and visualizations
- Analyze DJ performance patterns and trends
- Create booking insights tools
- Research electronic music history

### For Researchers
- Study venue programming patterns over 16+ years
- Analyze artist career trajectories
- Document the evolution of Berlin's Techno scene
- Academic research on club culture

### For Fans
- Discover which DJs have played Berghain/Panorama Bar
- Track favorite artists' performance history
- Explore the venue's complete lineup archive
- Plan visits based on resident DJ patterns

---

## 🐛 Issues & Contributions

Found a bug? Missing data? Want to suggest a feature?

<p align="center">
  <a href="https://github.com/M-Igashi/berghain-database/issues/new?template=bug_report.md">
    <img src="https://img.shields.io/badge/🐛_Report_Bug-red?style=for-the-badge" alt="Report Bug">
  </a>
  <a href="https://github.com/M-Igashi/berghain-database/issues/new?template=data_correction.md">
    <img src="https://img.shields.io/badge/📊_Data_Correction-blue?style=for-the-badge" alt="Data Correction">
  </a>
  <a href="https://github.com/M-Igashi/berghain-database/issues/new?template=feature_request.md">
    <img src="https://img.shields.io/badge/✨_Feature_Request-green?style=for-the-badge" alt="Feature Request">
  </a>
</p>

See [CONTRIBUTING.md](CONTRIBUTING.md) for detailed guidelines.

---

## 💖 Support This Project

This database is maintained as a labor of love for the Techno community. If you find it useful, consider supporting:

### Donate

<table>
<tr>
<td align="center">
<strong>Bitcoin (BTC)</strong><br>
<img src="https://berghain.ravers.workers.dev/qr/bitcoin.png" width="150"><br>
<code>bc1qp4lg5mw0c9nv3ygjnnx5gg95wk7h6flpw5pvfq</code>
</td>
<td align="center">
<strong>Solana (SOL)</strong><br>
<img src="https://berghain.ravers.workers.dev/qr/solana.png" width="150"><br>
<code>8oj2PMky2Zx9qznjK5eG7AUdqRab8GnrFJ7UamfEKRu</code>
</td>
</tr>
</table>

### Other Ways to Support

- ⭐ **Star this repository** — helps others discover the project
- 🐦 **Share on social media** — spread the word
- 📝 **Report issues** — help improve data quality
- 🔗 **Link to us** — mention the database in your projects

---

## 📄 License

This project is licensed under the MIT License — see the [LICENSE](LICENSE) file for details.

---

## 🙏 Acknowledgments

- **Berghain & Panorama Bar** — For being an incomparable cultural institution
- **The Artists** — Who created the music that defined a generation
- **Techno Community** — For preserving and celebrating this culture

---

<p align="center">
  <strong>Note</strong>: This is an unofficial project created by fans for fans.<br>
  Not affiliated with Berghain or Ostgut Ton.
</p>

<p align="center">
  <em>Built with ❤️ for the global Techno community</em>
</p>

<p align="center">
  <a href="https://berghain.ravers.workers.dev">
    <img src="https://img.shields.io/badge/Visit-berghain.ravers.workers.dev-black?style=for-the-badge" alt="Visit Site">
  </a>
</p>
