# 🎵 Berghain Klubnacht Database

A comprehensive database and API for tracking DJ performances at Berlin's legendary Berghain nightclub (2004-2009).

[![Website](https://img.shields.io/badge/Website-berghain.ravers.workers.dev-blue)](https://berghain.ravers.workers.dev)
[![API Status](https://img.shields.io/badge/API-Live-green)](https://berghain.ravers.workers.dev/health)
[![Issues](https://img.shields.io/badge/Issues-Welcome-orange)](https://github.com/M-Igashi/berghain-database/issues)

## 🏛️ About the Database

This project preserves the history of one of the world's most influential techno venues by cataloging DJ performances at Berghain and Panorama Bar from 2004 to 2009. The database contains:

- **2,070+ Artists** from around the world
- **725+ Events** (Klubnacht sessions)
- **10,137+ Performance records**
- **Historical flyers** and event documentation

### Database Schema

The database consists of three main tables:

#### Artists
```sql
id              INTEGER PRIMARY KEY
name            TEXT NOT NULL UNIQUE
country         TEXT
genre           TEXT  
first_appearance DATE
total_performances INTEGER DEFAULT 0
created_at      DATETIME DEFAULT CURRENT_TIMESTAMP
```

#### Events
```sql
id          INTEGER PRIMARY KEY
date        DATE NOT NULL
venue       TEXT NOT NULL  -- 'Berghain' or 'Panorama Bar'
event_type  TEXT DEFAULT 'Klubnacht'
created_at  DATETIME DEFAULT CURRENT_TIMESTAMP
```

#### Performances
```sql
id          INTEGER PRIMARY KEY
artist_id   INTEGER NOT NULL
event_id    INTEGER NOT NULL
venue       TEXT NOT NULL  -- 'Berghain' or 'Panorama Bar'
time_slot   TEXT           -- e.g., '22:00-04:00'
created_at  DATETIME DEFAULT CURRENT_TIMESTAMP
```

## 🚀 API Documentation

Base URL: `https://berghain.ravers.workers.dev`

### Public Endpoints

#### Database Statistics
```http
GET /api/stats
```

Returns overall database statistics including total artists, events, and performances.

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

#### Artist Rankings
```http
GET /api/artists/ranking
```

**Query Parameters:**
- `limit` (optional): Number of results to return (default: 50, max: 200)
- `venue` (optional): Filter by venue (`berghain` or `panorama`)
- `year` (optional): Filter by year (2004-2009)
- `country` (optional): Filter by artist country

**Response:**
```json
{
  "rankings": [
    {
      "rank": 1,
      "artist_id": 123,
      "name": "Artist Name",
      "country": "Germany",
      "genre": "Techno",
      "total_performances": 45,
      "berghain_performances": 25,
      "panorama_performances": 20,
      "first_appearance": "2004-09-25"
    }
  ],
  "filters": {
    "venue": null,
    "year": null,
    "country": null,
    "limit": 50
  }
}
```

#### All Artists
```http
GET /api/artists
```

**Query Parameters:**
- `search` (optional): Search by artist name
- `country` (optional): Filter by country
- `genre` (optional): Filter by genre
- `limit` (optional): Number of results (default: 100, max: 500)
- `offset` (optional): Pagination offset (default: 0)

#### Artist Details
```http
GET /api/artists/{artistId}
```

Returns detailed information about a specific artist including all performance history.

#### Events
```http
GET /api/events
```

**Query Parameters:**
- `year` (optional): Filter by year (2004-2009)
- `month` (optional): Filter by month (1-12)
- `venue` (optional): Filter by venue (`berghain` or `panorama`)
- `limit` (optional): Number of results (default: 50, max: 200)
- `offset` (optional): Pagination offset

#### Artist Performances
```http
GET /api/artists/{artistId}/performances
```

Returns all performances for a specific artist.

#### Available Years
```http
GET /api/years
```

Returns list of available years with event counts.

#### Data Period
```http
GET /api/period
```

Returns the date range covered by the database.

### RSS Feed
```http
GET /rss
```

RSS feed of recent database updates and new artist additions.

### Sitemap
```http
GET /sitemap.xml
```

XML sitemap for search engine indexing.

### Health Check
```http
GET /health
```

API health status and performance metrics.

## 📊 Features

- **Artist Rankings**: Comprehensive rankings by performance count
- **Venue Comparison**: Berghain vs Panorama Bar statistics  
- **Historical Timeline**: Events organized by date and venue
- **Search & Filtering**: Advanced search capabilities
- **Performance Analytics**: Detailed performance history tracking
- **Real-time API**: Fast, cached responses with 99.9% uptime
- **SEO Optimized**: Full sitemap and RSS feed support

## 🌍 Usage Examples

### Get Top 10 Berghain Artists
```bash
curl "https://berghain.ravers.workers.dev/api/artists/ranking?venue=berghain&limit=10"
```

### Search for German Techno Artists
```bash
curl "https://berghain.ravers.workers.dev/api/artists?country=Germany&genre=Techno"
```

### Get Events from 2007
```bash
curl "https://berghain.ravers.workers.dev/api/events?year=2007"
```

### Get Artist Performance History
```bash
curl "https://berghain.ravers.workers.dev/api/artists/123/performances"
```

## 🔧 Technical Details

- **Infrastructure**: Cloudflare Workers + D1 Database
- **Performance**: Edge-cached responses, <100ms average response time
- **Uptime**: 99.9% availability with global CDN
- **Rate Limiting**: Fair use policy, no strict limits for public API
- **CORS**: Enabled for cross-origin requests

## 💫 About Berghain

Berghain is arguably the world's most famous techno club, located in a former East Berlin power plant. Known for its:

- Uncompromising techno music programming
- Strict door policy and no-photos rule  
- Industrial atmosphere in a converted power station
- Two main floors: Berghain (main floor) and Panorama Bar
- Legendary weekend-long Klubnacht sessions

This database preserves the early golden era (2004-2009) when many of today's techno legends established their careers at this iconic venue.

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

This project is maintained as a labor of love for the techno community. If you find it useful, consider supporting:

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
- **Resident Advisor** - Historical event data source
- **Techno Community** - For preserving and celebrating this culture

---

**Note**: This is an unofficial project created by fans for fans. Not affiliated with Berghain or Ostgut Ton.

*Built with ❤️ for the global techno community*
