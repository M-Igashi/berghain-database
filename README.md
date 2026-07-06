# Berghain Klubnacht Database

The most comprehensive historical archive of DJ performances at Berlin's Berghain nightclub — every Klubnacht since the club opened on **18 December 2004**.

**Live**: [berghain.ravers.workers.dev](https://berghain.ravers.workers.dev)

[![API Status](https://img.shields.io/badge/API_Status-Live-00d26a?style=flat-square&logo=cloudflare)](https://berghain.ravers.workers.dev/health)
[![REST API](https://img.shields.io/badge/API-RESTful-blue?style=flat-square)](#api-overview)
[![x402](https://img.shields.io/badge/x402-exports_%26_flyers-purple?style=flat-square)](docs/x402-ai-paywall.md)
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

This project preserves the history of one of the world's most influential Techno institutions by cataloging every DJ performance at Berghain and Panorama Bar.

The **2004–2009** era is reconstructed from the official monthly flyer archive (61 flyers), digitized and visually verified against the original PDFs — down to the label credits and artist descriptions printed on each flyer. **November 2009 onwards** comes directly from [berghain.berlin](https://www.berghain.berlin/) event listings. Artist names are normalized and cross-referenced against Resident Advisor and Discogs; aliases and units are consolidated the way the flyers themselves billed them. Lab.oratory, Garden, and Säule performances are excluded, as they are not considered core Klubnacht events.

## Tech Stack

| Component | Technology |
| --- | --- |
| Runtime | [Cloudflare Workers](https://workers.cloudflare.com/) |
| Framework | [Hono](https://hono.dev/) (TypeScript) |
| Database | Cloudflare D1 (SQLite) |
| Cache | 3-tier: Memory → Workers KV → D1 |
| Storage | Cloudflare R2 (flyer PDFs, QR codes) |
| Analytics | Cloudflare Analytics Engine (crawl stats & revenue tracking) |
| Payments | [x402 protocol](https://x402.org) — USDC on Solana mainnet via [Coinbase CDP](https://docs.cdp.coinbase.com/) facilitator |

## Documentation

| Document | Description |
| --- | --- |
| [API Reference](docs/api.md) | Full endpoint documentation with examples |
| [Architecture](docs/architecture.md) | System design, caching strategy, and performance |
| [x402 & AI Access](docs/x402-ai-paywall.md) | Freemium access model and x402 micropayments |
| [Database Schema](docs/schema.md) | Table definitions and relationships |
| [Aliases & Collective Acts](docs/aliases-and-units.md) | How DJ aliases (`aka`) and duo/collective members are represented |

## API Overview

**Base URL**: `https://berghain.ravers.workers.dev`

The API is **free and open for everyone** — no accounts, no API keys, CORS enabled. Only two things are paid, via the [x402 protocol](docs/x402-ai-paywall.md), for **all** requests: bulk exports (`/api/export/*`, $0.10) and historical flyer PDFs (`/flyers/*`, $0.01).

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

### Key Endpoints

| Endpoint | Description |
| --- | --- |
| `GET /api/stats` | Database statistics and venue breakdown |
| `GET /api/artists/ranking` | Top artists by performance count |
| `GET /api/artists?search=` | Search artists by name |
| `GET /api/artists/by-name/:name` | Exact artist lookup (URL-encoded name) |
| `GET /api/artists/:id/performances` | Artist's full performance history |
| `GET /api/residents/current` | Current active resident DJs |
| `GET /api/shows` | Browse all events |
| `GET /api/flyers` | Index of the 2004–2009 flyer archive |
| `GET /api/export/*` | Bulk export — JSON/CSV ($0.10 via x402) |

See [docs/api.md](docs/api.md) for the complete API reference.

### Content Negotiation & AI Discovery

All HTML pages return structured Markdown when requested with `Accept: text/markdown`, optimized for AI/LLM consumption. Machine-readable entry points:

- [`/llms.txt`](https://berghain.ravers.workers.dev/llms.txt) and [`/llms-full.txt`](https://berghain.ravers.workers.dev/llms-full.txt) — LLM discovery files
- [`/openapi.json`](https://berghain.ravers.workers.dev/openapi.json) — OpenAPI 3 specification with x402 price annotations
- [`/.well-known/x402`](https://berghain.ravers.workers.dev/.well-known/x402) — x402 payment manifest
- `/.well-known/agentic-capabilities.json` — agent capability descriptor

## Access Model: Freemium + x402

Almost everything is **free for everyone, including AI crawlers** — this is deliberate, to maximize the archive's visibility in AI search. Free API responses also carry x402 *signal* headers so agents can discover the paid routes.

```
Request
  ├── /api/export/*, /flyers/*  → x402 paywall (all requests)
  │       ├── no payment  → HTTP 402 Payment Required
  │       └── paid        → HTTP 200
  └── everything else          → free (x402 signal headers for AI crawlers)
```

| What | Price | Applies to |
| --- | --- | --- |
| Bulk exports (`/api/export/artists\|events\|performances`, JSON/CSV) | $0.10 | all requests |
| Historical flyer PDFs (`/flyers/*.pdf`) | $0.01 | all requests |
| Everything else (stats, rankings, artist & event data, HTML pages) | Free | everyone |

Payments settle in **USDC on Solana mainnet** through the [Coinbase Developer Platform (CDP)](https://docs.cdp.coinbase.com/) facilitator. AI crawler activity — free and paid — is tracked via Cloudflare Analytics Engine and published on the [public transparency dashboard](https://berghain.ravers.workers.dev/dashboard/crawl-stats).

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
- **Data**: [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/) — free to use, share, and adapt; please credit [berghain.ravers.workers.dev](https://berghain.ravers.workers.dev) as the source.

---

*This is an unofficial community project. Not affiliated with Berghain or Ostgut Ton.*
