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
  <a href="#-use-this-database">Use This Database</a> •
  <a href="#-get-involved">Get Involved</a> •
  <a href="#-api-documentation">API Docs</a> •
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

> **Coverage Period**: November 2009 - February 2026 (ongoing monthly updates)

This project preserves the history of one of the world's most influential Techno institutions by cataloging every DJ performance at Berghain and Panorama Bar.

---

## 🛠️ Use This Database

This database provides a **free public REST API** — no authentication required. Whether you're building an app, conducting research, or exploring data, here's how different users can benefit:

### 👩‍💻 For Developers

Build applications powered by 16+ years of Berghain performance data:

```bash
# Get top performers of all time
curl "https://berghain.ravers.workers.dev/api/artists/ranking?limit=20"

# Search for any artist
curl "https://berghain.ravers.workers.dev/api/artists?search=ellen+allien"

# Get an artist's complete performance history
curl "https://berghain.ravers.workers.dev/api/artists/16/performances"

# Fetch current active residents
curl "https://berghain.ravers.workers.dev/api/residents/current"
```

**Example Projects:**
- DJ performance analytics dashboards
- Artist career trajectory visualizations
- Booking frequency analysis tools
- Historical trends explorer
- Techno scene network graphs

### 🎓 For Researchers

Study one of the world's most documented club venues:

| Research Area | Relevant Endpoints |
|--------------|-------------------|
| Venue programming patterns | `/api/stats/monthly`, `/api/shows` |
| Artist career analysis | `/api/artists/:id/stats`, `/api/artists/:id/performances` |
| Resident DJ dynamics | `/api/residents/current` |
| Historical trends | `/api/years`, `/api/period`, `/api/artists/ranking/year/:year` |

**Academic Use Cases:**
- Club culture and nightlife studies
- Electronic music history documentation
- Social network analysis of DJ communities
- Urban cultural institution research
- Gender and diversity analysis in DJ bookings

### 🎧 For Fans & Enthusiasts

Explore the complete Berghain archive:

- **Discover** which DJs have played the most sets
- **Track** your favorite artists' performance history
- **Compare** Berghain vs Panorama Bar bookings
- **Find** when artists first/last played
- **Explore** monthly and yearly programming patterns

---

## 🤝 Get Involved

This database is maintained by the community, for the community. Your contributions help preserve Techno history.

<p align="center">
  <a href="https://github.com/M-Igashi/berghain-database/issues/new?template=data_correction.md">
    <img src="https://img.shields.io/badge/📊_Report_Missing_Data-blue?style=for-the-badge" alt="Report Missing Data">
  </a>
  <a href="https://github.com/M-Igashi/berghain-database/issues/new?template=bug_report.md">
    <img src="https://img.shields.io/badge/🐛_Report_Bug-red?style=for-the-badge" alt="Report Bug">
  </a>
  <a href="https://github.com/M-Igashi/berghain-database/issues/new?template=feature_request.md">
    <img src="https://img.shields.io/badge/✨_Request_Feature-green?style=for-the-badge" alt="Request Feature">
  </a>
</p>

### How You Can Help

| Contribution | Description |
|-------------|-------------|
| 🔍 **Data Corrections** | Found a wrong date, misspelled artist, or incorrect venue? Let us know! |
| ➕ **Missing Performances** | Know about a performance that's not in the database? Report it with source |
| 💡 **Feature Ideas** | Have an idea for a new API endpoint or analysis? We'd love to hear it |
| ⭐ **Star & Share** | Help others discover this project |

See [CONTRIBUTING.md](CONTRIBUTING.md) for detailed guidelines on submitting corrections with proper sources.

---

## 💖 Support This Project

This database is a labor of love, maintained for the global Techno community. If you find it useful:

<table>
<tr>
<td align="center">
<strong>Bitcoin (BTC)</strong><br>
<img src="https://berghain.ravers.workers.dev/qr/bitcoin.png" width="120"><br>
<code style="font-size: 10px;">bc1qp4lg5mw0c9nv3ygjnnx5gg95wk7h6flpw5pvfq</code>
</td>
<td align="center">
<strong>Solana (SOL)</strong><br>
<img src="https://berghain.ravers.workers.dev/qr/solana.png" width="120"><br>
<code style="font-size: 10px;">8oj2PMky2Zx9qznjK5eG7AUdqRab8GnrFJ7UamfEKRu</code>
</td>
<td align="center">
<strong>Other Ways</strong><br><br>
⭐ Star this repo<br>
🐦 Share on social<br>
📝 Report issues<br>
🔗 Link to us
</td>
</tr>
</table>

---

## 📚 API Documentation

**Base URL**: `https://berghain.ravers.workers.dev`

### Quick Reference

| Endpoint | Description |
|----------|-------------|
| `GET /api/stats` | Database statistics & venue breakdown |
| `GET /api/artists/ranking` | Top artists by performance count |
| `GET /api/artists?search=` | Search artists by name |
| `GET /api/artists/:id` | Get artist details |
| `GET /api/artists/:id/stats` | Detailed artist statistics |
| `GET /api/artists/:id/performances` | Artist's performance history |
| `GET /api/shows` | Browse all events |
| `GET /api/residents/current` | Current active residents |
| `GET /api/years` | Available years in database |
| `GET /api/period` | Data coverage period |
| `GET /api/stats/monthly?year=` | Monthly statistics by year |

### Detailed Endpoints

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
  }
]
```
</details>

<details>
<summary><code>GET /api/artists</code> — Search Artists</summary>

Search and browse all artists with pagination.

**Query Parameters:**
| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `search` | string | — | Search term (supports special chars: Ø, ø, À-ÿ) |
| `page` | integer | 1 | Page number |
| `limit` | integer | 20 | Results per page |

**Example:**
```bash
curl "https://berghain.ravers.workers.dev/api/artists?search=rødhåd"
```
</details>

<details>
<summary><code>GET /api/artists/:id/stats</code> — Artist Statistics</summary>

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
<summary><code>GET /api/artists/:id/performances</code> — Performance History</summary>

Get all performances for a specific artist.

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

<details>
<summary><code>GET /api/shows</code> — Browse Events</summary>

Browse all Klubnacht events.

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
    "total_artists": 14
  }
]
```
</details>

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

### Additional Endpoints

| Endpoint | Description |
|----------|-------------|
| `GET /api/artists/by-name/:name` | Find artist by exact name (URL-encoded) |
| `GET /api/artists/ranking/year/:year` | Rankings for a specific year |
| `GET /sitemap.xml` | XML sitemap for search engines |
| `GET /feed.xml` | RSS feed of recent events |
| `GET /health` | API health check |

### API Features

- **CORS enabled** — Works from any origin
- **No authentication** — Completely free and open
- **Generous rate limits** — Fair use policy
- **Fast responses** — <100ms average (95%+ cache hit rate)
- **ISO dates** — Machine-readable date formats
- **Special character support** — Handles Ø, ø, À-ÿ, etc.

---

## 🗄️ Database Schema

SQLite database (Cloudflare D1) with three core tables:

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

<details>
<summary>View detailed schema</summary>

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

</details>

---

## 🏗️ Architecture

<details>
<summary>View technical architecture</summary>

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
│  │                           │  │  (Static Assets)          │  │
│  │  L1: In-Memory (2-24h)    │  └───────────────────────────┘  │
│  │  L2: KV Storage (7-30d)   │                                 │
│  │  L3: D1 Database          │                                 │
│  └───────────────────────────┘                                 │
└─────────────────────────────────────────────────────────────────┘
```

### 3-Tier Cache System

The API implements an advanced caching strategy that reduces database reads by **~95%**:

| Layer | Storage | Scope | TTL |
|-------|---------|-------|-----|
| **L1** | In-Memory Map | Per-instance | 2-24h |
| **L2** | Workers KV | Global | 7-30 days |
| **L3** | D1 Database | Global | Permanent |

### Key Technologies

| Component | Technology |
|-----------|------------|
| Runtime | Cloudflare Workers |
| Framework | Hono (TypeScript) |
| Database | Cloudflare D1 (SQLite) |
| Cache | Workers KV |
| Storage | Cloudflare R2 |

### Performance

| Metric | Value |
|--------|-------|
| Average Response Time | <100ms |
| Cache Hit Rate | >95% |
| Global Availability | 99.9%+ |
| Edge Locations | 300+ cities |

</details>

---

## 📄 License

MIT License — see [LICENSE](LICENSE) for details.

---

## 🙏 Acknowledgments

- **Berghain & Panorama Bar** — For being an incomparable cultural institution
- **The Artists** — Who created the music that defined a generation
- **The Community** — For preserving and celebrating Techno culture

---

<p align="center">
  <strong>Note</strong>: This is an unofficial community project. Not affiliated with Berghain or Ostgut Ton.
</p>

<p align="center">
  <em>Built with ❤️ for the global Techno community</em>
</p>

<p align="center">
  <a href="https://berghain.ravers.workers.dev">
    <img src="https://img.shields.io/badge/Visit-berghain.ravers.workers.dev-black?style=for-the-badge" alt="Visit Site">
  </a>
</p>
