# Berghain Klubnacht Database

The most comprehensive historical archive of DJ performances at Berlin's Berghain nightclub — every club night in the Saturday Klubnacht slot since the club opened on **18 December 2004**.

**Live**: [berghain.ravers.workers.dev](https://berghain.ravers.workers.dev)

[![API Status](https://img.shields.io/badge/API_Status-Live-00d26a?style=flat-square&logo=cloudflare)](https://berghain.ravers.workers.dev/health)
[![REST API](https://img.shields.io/badge/API-RESTful-blue?style=flat-square)](#api-overview)
[![x402](https://img.shields.io/badge/x402-bulk_exports-purple?style=flat-square)](docs/x402-ai-paywall.md)
[![Code License](https://img.shields.io/badge/Code-MIT-yellow?style=flat-square)](LICENSE)
[![Data License](https://img.shields.io/badge/Data-CC_BY_4.0-orange?style=flat-square)](https://creativecommons.org/licenses/by/4.0/)

---

## Database Overview

| Metric | Value |
| --- | --- |
| Artists | 2,500+ |
| Events | 1,000+ |
| Performances | 13,000+ |
| Coverage | 18 December 2004 (opening night) – present (ongoing) |

This project preserves the history of one of the world's most influential Techno institutions by cataloging every club night in the Saturday Klubnacht slot — Klubnacht itself plus SNAX Club, label nights, anniversaries and holiday specials — on the Berghain main floor and Panorama Bar. See the [Data Policy](docs/data-policy.md) for exactly what counts.

The **2004–2009** era is reconstructed from the official monthly flyer archive (61 flyers), digitized and visually verified against the original PDFs — down to the label credits and artist descriptions printed on each flyer. **November 2009 onwards** comes directly from [berghain.berlin](https://www.berghain.berlin/) event listings. Artist names are normalized and cross-referenced against Resident Advisor and Discogs; aliases and units are consolidated the way the flyers themselves billed them. Lab.oratory, Säule, Garten/Halle and Kantine performances are excluded, along with Friday series and Panorama-Bar-only holiday specials — see the [Data Policy](docs/data-policy.md) for the full inclusion rules.

## Tech Stack

Everything runs on Cloudflare's edge. There is no origin server, no container, and no frontend framework.

### Platform

| Layer | Technology | Role |
| --- | --- | --- |
| Runtime | [Cloudflare Workers](https://workers.cloudflare.com/) | One Worker serves the API and every page |
| Language | TypeScript 7 | |
| Framework | [Hono](https://hono.dev/) 4 | Routing and middleware |
| Database | Cloudflare D1 (SQLite) | Artists, events, performances |
| Cache | In-memory → Workers KV → D1 | 3-tier response cache |
| Storage | Cloudflare R2, Workers Static Assets | QR codes; font, icons, and share image |
| Scheduling | Cron Trigger | Daily cache warmup |

### Frontend

| Layer | Technology | Role |
| --- | --- | --- |
| Rendering | Server-rendered HTML from TypeScript templates | No client framework, no build step |
| Typography | Self-hosted JetBrains Mono | No Google Fonts or other third-party requests on any content page |
| AI-readable | Markdown for every page via `Accept: text/markdown`, `llms.txt`, OpenAPI 3.1 | Built for agents as well as browsers |

### Operations

| Layer | Technology | Role |
| --- | --- | --- |
| Payments | [x402 protocol](https://x402.org) v2 (`@x402/hono`) | USDC on Solana or Base through the [Coinbase CDP](https://docs.cdp.coinbase.com/) facilitator |
| Analytics | Cloudflare Analytics Engine | AI crawler stats and x402 revenue. No visitor tracking |
| Observability | Workers Logs and Traces | |
| Search indexing | IndexNow, XML sitemap, RSS | |
| Deployment | Wrangler | Manual deploys |
| Security review | [Claude Code](https://claude.com/claude-code) (Claude Fable 5.2) | Reviews of code and configuration changes |

See [Architecture](docs/architecture.md) for how the pieces fit together.

## Documentation

| Document | Description |
| --- | --- |
| [API Reference](docs/api.md) | Quick start, endpoint index, integration guide, attribution, and every endpoint in detail |
| [Architecture](docs/architecture.md) | System design, caching strategy, and performance |
| [x402 & AI Access](docs/x402-ai-paywall.md) | Freemium access model and x402 micropayments |
| [Database Schema](docs/schema.md) | Table definitions and relationships |
| [Data Policy](docs/data-policy.md) | What counts as a performance: included/excluded event types, floors, sources |
| [Aliases & Collective Acts](docs/aliases-and-units.md) | How DJ aliases (`aka`) and duo/collective members are represented |

## API Overview

**Base URL**: `https://berghain.ravers.workers.dev`

The API is **free and open for everyone**: no accounts, no API keys, CORS enabled. Only two things are paid, via the [x402 protocol](docs/x402-ai-paywall.md), for **all** requests: bulk exports (`/api/export/*`, $0.10) and batch requests for more than 20 artists (`/api/performances`, $0.01).

```bash
# Who plays next, floor by floor
curl "https://berghain.ravers.workers.dev/api/shows/upcoming"

# Top performers of all time
curl "https://berghain.ravers.workers.dev/api/artists/ranking?limit=20"

# Search for any artist
curl "https://berghain.ravers.workers.dev/api/artists?search=ellen+allien"

# An artist's complete performance history
curl "https://berghain.ravers.workers.dev/api/artists/16/performances"

# Current residents, split by floor
curl "https://berghain.ravers.workers.dev/api/residents/current"
```

### Endpoints

| | Endpoint | Description |
| --- | --- | --- |
| **Artists** | `GET /api/artists?search=` | List or search artists (`limit=0` for all) |
| | `GET /api/artists/:id` | One artist by id |
| | `GET /api/artists/by-name/:name` | One artist by exact name (URL-encoded) |
| | `GET /api/artists/:id/stats` | Career statistics |
| | `GET /api/artists/:id/performances` | Full performance history |
| | `GET /api/performances?artist_ids=` | Histories of up to 20 artists in one request (21 to 200 for $0.01) |
| **Rankings** | `GET /api/artists/ranking` | All-time ranking, optionally per floor |
| | `GET /api/artists/ranking/year/:year` | Ranking for one year |
| | `GET /api/residents/current` | Most active artists of the past 3 years, split by floor |
| **Events** | `GET /api/shows` | All nights, filterable by year and month |
| | `GET /api/shows/upcoming` | Upcoming nights with the lineup of each floor |
| **Reference** | `GET /api/stats`, `GET /api/stats/monthly` | Totals, floor split, events per month |
| | `GET /api/years`, `GET /api/period` | Years covered, first and last date |
| | `GET /api/flyers` | Index of the 2004–2009 flyer archive, with links to the official PDFs |
| **Paid** | `GET /api/export/*` | Bulk export, JSON or CSV ($0.10 via x402) |

Artist responses include `page_url`, the artist's page on this site. If you show an artist's history on your own site, please link to it (see [Attribution](docs/api.md#attribution)). The [Integration Guide](docs/api.md#integration-guide) shows how to do this in two requests per artist.

Building something and missing an endpoint? Several of these exist because someone asked. [Open a feature request](https://github.com/M-Igashi/berghain-database/issues/new?template=feature_request.md) before you start scraping: one request shaped for your use case is easier for you and lighter on the database.

See [docs/api.md](docs/api.md) for the complete API reference.

### Content Negotiation & AI Discovery

All content pages return structured Markdown when requested with `Accept: text/markdown`, optimized for AI/LLM consumption. Machine-readable entry points:

| Entry point | Description |
| --- | --- |
| [`/llms.txt`](https://berghain.ravers.workers.dev/llms.txt), [`/llms-full.txt`](https://berghain.ravers.workers.dev/llms-full.txt) | LLM discovery files |
| [`/openapi.json`](https://berghain.ravers.workers.dev/openapi.json) | OpenAPI 3.1 specification with x402 price annotations |
| [`/.well-known/x402`](https://berghain.ravers.workers.dev/.well-known/x402) | x402 payment manifest |
| `/.well-known/agentic-capabilities.json` | Agent capability descriptor |

## Access Model: Freemium + x402

Almost everything is **free for everyone, including AI crawlers**. This is deliberate, to maximize the archive's visibility in AI search. The price follows what a request costs the database, not the kind of content.

```
Request
  ├── /api/export/*                        → x402 paywall (all requests)
  ├── /api/performances with 21–200 ids    → x402 paywall (all requests)
  │       ├── no payment  → HTTP 402 Payment Required
  │       └── paid        → HTTP 200
  └── everything else                      → free
```

| What | Price | Applies to |
| --- | --- | --- |
| Bulk exports (`/api/export/artists\|events\|performances`, JSON/CSV) | $0.10 | all requests |
| Batch performance histories for 21 to 200 artists (`/api/performances`) | $0.01 | all requests |
| Everything else (stats, rankings, artist and event data, batches up to 20 artists, HTML pages) | Free | everyone |

Payments settle in **USDC on Solana or Base** through the [Coinbase Developer Platform (CDP)](https://docs.cdp.coinbase.com/) facilitator, using x402 v2. The original flyer PDFs are not sold or hosted here: `/api/flyers` links to the official copies on berghain.berlin. AI crawler activity, free and paid, is tracked via Cloudflare Analytics Engine and published on the [public transparency dashboard](https://berghain.ravers.workers.dev/dashboard/crawl-stats).

See [docs/x402-ai-paywall.md](docs/x402-ai-paywall.md) for details.

## Get Involved

This database is maintained by the community, and community input is a large part of what keeps it accurate — special thanks to the [r/Berghain_Community](https://www.reddit.com/r/Berghain_Community/) subreddit for corrections, lineup knowledge, and identity research.

- [Report missing data](https://github.com/M-Igashi/berghain-database/issues/new?template=data_correction.md)
- [Report a bug](https://github.com/M-Igashi/berghain-database/issues/new?template=bug_report.md)
- [Request a feature](https://github.com/M-Igashi/berghain-database/issues/new?template=feature_request.md)

See [CONTRIBUTING.md](CONTRIBUTING.md) for detailed guidelines on submitting corrections with proper sources.

## Privacy

**No ads. No cookies. No trackers. We never fuck your privacy.** The site sets zero cookies, runs no advertising, and embeds no third-party analytics or tracking scripts. AI-crawler statistics are aggregated server-side only — nothing about human visitors is collected, profiled, or sold.

## Support

This database is a labor of love, maintained for the global Techno community.

| Bitcoin (BTC) | Solana (SOL) |
| --- | --- |
| `bc1qp4lg5mw0c9nv3ygjnnx5gg95wk7h6flpw5pvfq` | `8oj2PMky2Zx9qznjK5eG7AUdqRab8GnrFJ7UamfEKRu` |

## License

- **Code**: [MIT](LICENSE)
- **Data**: [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/). Free to use, share, and adapt, including commercially, as long as you credit **Berghain Klubnacht Database** with a link to [berghain.ravers.workers.dev](https://berghain.ravers.workers.dev). See [Attribution](docs/api.md#attribution).

---

*This is an unofficial community project. Not affiliated with Berghain or Ostgut Ton.*
