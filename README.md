# 🎵 Berghain Klubnacht Database

A comprehensive database and API for tracking DJ performances at Berlin's legendary Berghain nightclub (2009-2025).
[berghain.ravers.workers.dev](https://berghain.ravers.workers.dev)

[![Website](https://img.shields.io/badge/Website-berghain.ravers.workers.dev-blue)](https://berghain.ravers.workers.dev)
[![API Status](https://img.shields.io/badge/API-Live-green)](https://berghain.ravers.workers.dev/health)
[![Issues](https://img.shields.io/badge/Issues-Welcome-orange)](https://github.com/M-Igashi/berghain-database/issues)

## 🏛️ About the Database

This project preserves the history of one of the world's most influential Techno venues by cataloging DJ performances at Berghain and Panorama Bar from **November 2009 to Today**. The database contains:

- **2,070+ Artists** from around the world
- **725+ Events** (Klubnacht sessions)
- **10,137+ Performance records**
- **16+ Years** of continuous documentation

### Database Schema

The database consists of three core tables:

#### Artists
```sql
id                     INTEGER PRIMARY KEY AUTOINCREMENT
name                   TEXT UNIQUE NOT NULL
normalized_name        TEXT                    -- Normalized for search
total_performances     INTEGER DEFAULT 0
berghain_performances  INTEGER DEFAULT 0
panorama_performances  INTEGER DEFAULT 0
```

#### Events
```sql
id            INTEGER PRIMARY KEY AUTOINCREMENT  
event_id      INTEGER UNIQUE NOT NULL            -- Original Berghain event ID
title         TEXT NOT NULL                      -- Usually "Klubnacht"
date          TEXT NOT NULL                      -- DD.MM.YYYY format
iso_date      TEXT                               -- YYYY-MM-DD format
year          INTEGER
month         INTEGER
url           TEXT NOT NULL                      -- Original event URL
total_artists INTEGER DEFAULT 0
```

#### Performances
```sql
id        INTEGER PRIMARY KEY AUTOINCREMENT
event_id  INTEGER NOT NULL                    -- References events(id)
artist_id INTEGER NOT NULL                    -- References artists(id)  
venue     TEXT NOT NULL                       -- 'Berghain' or 'Panorama Bar'
```

## 🚀 API Documentation

Base URL: `https://berghain.ravers.workers.dev`

### Core Statistics

#### Database Statistics
```http
GET /api/stats
```

Returns overall database statistics.

**Response:**
```json
{
  "total_artists": 2070,
  "total_events": 725,
  "total_performances": 10137,
  "venue_breakdown": [
    {"venue": "Berghain", "count": 5416},
    {"venue": "Panorama Bar", "count": 4721}
  ]
}
```

#### Data Coverage Period
```http
GET /api/period
```

**Response:**
```json
{
  "start_date": "2009-11-07",
  "end_date": "2025-09-27"
}
```

#### Available Years
```http
GET /api/years
```

**Response:**
```json
[2025, 2024, 2023, 2022, 2021, 2020, 2019, 2018, 2017, 2016, 2015, 2014, 2013, 2012, 2011, 2010, 2009]
```

### Artist Endpoints

#### Artist Rankings
```http
GET /api/artists/ranking
```

Returns artists ranked by performance count (default: top 50).

**Query Parameters:**
- `limit` (optional): Number of results to return (default: 50)

**Response:**
```json
[
  {
    "id": 14,
    "name": "Norman Nodge",
    "total_performances": 146,
    "berghain_performances": 139,
    "panorama_performances": 7
  },
  {
    "id": 16,
    "name": "Ben Klock",
    "total_performances": 133,
    "berghain_performances": 125,
    "panorama_performances": 8
  }
]
```

#### Artist Search & Listing
```http
GET /api/artists
```

Search and browse all artists with pagination.

**Query Parameters:**
- `search` (optional): Search term (supports normalized characters: ¥, Ø, ø, À-ÿ)
- `page` (optional): Page number (default: 1)
- `limit` (optional): Results per page (default: 20)

**Response:** Same format as `/api/artists/ranking`

#### Artist by ID
```http
GET /api/artists/{artistId}
```

Get basic artist information.

**Response:**
```json
{
  "id": 16,
  "name": "Ben Klock",
  "total_performances": 133,
  "berghain_performances": 125,
  "panorama_performances": 8
}
```

#### Artist by Name
```http
GET /api/artists/by-name/{artistName}
```

Find artist by exact name match (URL-encoded).

**Example:** `/api/artists/by-name/Ben%20Klock`

#### Artist Statistics
```http
GET /api/artists/{artistId}/stats
```

Get detailed statistics for an artist.

**Response:**
```json
{
  "name": "Ben Klock",
  "total_performances": 133,
  "berghain_performances": 125,
  "panorama_performances": 8,
  "first_performance": "2009-12-12",
  "last_performance": "2025-08-30",
  "active_years": 17,
  "recent_performances_2years": 18
}
```

#### Artist Performance History
```http
GET /api/artists/{artistId}/performances
```

Get all performances for a specific artist.

**Query Parameters:**
- `limit` (optional): Number of results to return

**Response:**
```json
[
  {
    "title": "Klubnacht",
    "date": "30.08.2025",
    "iso_date": "2025-08-30",
    "url": "https://www.berghain.berlin/de/event/79230/",
    "venue": "Panorama Bar",
    "artist_name": "Ben Klock"
  }
]
```

### Event Endpoints

#### Events Listing
```http
GET /api/shows
```

Browse all Klubnacht events with filtering.

**Query Parameters:**
- `limit` (optional): Number of results (default: 20)

**Response:**
```json
[
  {
    "id": 889,
    "event_id": 79234,
    "title": "Klubnacht",
    "date": "27.09.2025",
    "iso_date": "2025-09-27",
    "year": 2025,
    "month": 9,
    "url": "https://www.berghain.berlin/de/event/79234/",
    "total_artists": 15,
    "actual_performances": 15
  }
]
```

### Resident Analysis

#### Current Residents
```http
GET /api/residents/current
```

Returns current active residents based on 3-year performance analysis.

**Response:**
```json
[
  {
    "id": 36,
    "name": "Steffi",
    "total_performances_3y": 30,
    "recent_performances_1y": 9,
    "last_performance": "2025-09-27",
    "total_performances": 121,
    "berghain_performances": 55,
    "panorama_performances": 66
  }
]
```

### Monthly Analytics

#### Monthly Statistics
```http
GET /api/stats/monthly
```

Get monthly performance statistics by year.

**Query Parameters:**
- `year` (optional): Specific year to analyze (default: current year)

**Response:**
```json
[
  {
    "month": 1,
    "event_count": 60,
    "total_artists": 730,
    "avg_artists_per_event": 12.17
  }
]
```

## 🌍 Usage Examples

### Get Top 10 Artists
```bash
curl "https://berghain.ravers.workers.dev/api/artists/ranking?limit=10"
```

### Search for Artists
```bash
# Search by name
curl "https://berghain.ravers.workers.dev/api/artists?search=ben+klock"

# Find exact artist
curl "https://berghain.ravers.workers.dev/api/artists/by-name/Ben%20Klock"
```

### Get Artist Performance History
```bash
curl "https://berghain.ravers.workers.dev/api/artists/16/performances"
```

### Get Current Residents
```bash
curl "https://berghain.ravers.workers.dev/api/residents/current"
```

### Analyze Monthly Trends
```bash
curl "https://berghain.ravers.workers.dev/api/stats/monthly?year=2025"
```

## 🔧 Technical Details

- **Infrastructure**: Cloudflare Workers + D1 Database + R2 Storage
- **Performance**: Edge-cached responses, <100ms average response time
- **Uptime**: 99.9% availability with global CDN distribution
- **Rate Limiting**: Fair use policy, generous limits for public API
- **CORS**: Enabled for cross-origin requests
- **Search**: Advanced normalization supporting special characters (¥, Ø, ø, À-ÿ)

## 🐛 Issues & Feedback

Found a bug? Missing data? Want to suggest a feature?

[**Open an Issue**](https://github.com/M-Igashi/berghain-database/issues) - We welcome all feedback!

**Common Issue Types:**
- 🐛 Bug reports
- 📊 Data corrections or additions  
- ✨ Feature requests
- 📖 Documentation improvements
- 🤔 General questions

## 💖 Support This Project

This project is maintained as a labor of love for the Techno community. If you find it useful, consider supporting:

### 💰 Donations

**Bitcoin (BTC)**
```
bc1qp4lg5mw0c9nv3ygjnnx5gg95wk7h6flpw5pvfq
```

**Solana (SOL)**  
```
8oj2PMky2Zx9qznjK5eG7AUdqRab8GnrFJ7UamfEKRu
```

### 🌟 Other Ways to Support

- ⭐ Star this repository
- 🐦 Share on social media  
- 📝 Report bugs or suggest improvements
- 🎵 Visit [berghain.ravers.workers.dev](https://berghain.ravers.workers.dev)

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 🙏 Acknowledgments

- **Berghain & Panorama Bar** - For being an incomparable cultural institution
- **The Artists** - Who created the music that defined a generation
- **Techno Community** - For preserving and celebrating this culture

---

**Note**: This is an unofficial project created by fans for fans. Not affiliated with Berghain or Ostgut Ton.

*Built with ❤️ for the global Techno community*
