# Berghain Klubnacht Database

The most comprehensive historical archive of DJ performances at Berlin's Berghain nightclub.

**Live**: [berghain.ravers.workers.dev](https://berghain.ravers.workers.dev)

[![API Status](https://img.shields.io/badge/API_Status-Live-00d26a?style=flat-square&logo=cloudflare)](https://berghain.ravers.workers.dev/health)
[![REST API](https://img.shields.io/badge/API-RESTful-blue?style=flat-square)](#api-overview)
[![x402](https://img.shields.io/badge/x402-AI_Paywall-purple?style=flat-square)](docs/x402-ai-paywall.md)
[![License](https://img.shields.io/badge/License-MIT-yellow?style=flat-square)](LICENSE)

---

## Database Overview

| Metric | Value |
| --- | --- |
| Artists | 2,099+ |
| Events | 748+ |
| Performances | 10,466+ |
| Coverage | November 2009 – present (ongoing) |

This project preserves the history of one of the world's most influential Techno institutions by cataloging every DJ performance at Berghain and Panorama Bar.

## Tech Stack

| Component | Technology |
| --- | --- |
| Runtime | [Cloudflare Workers](https://workers.cloudflare.com/) |
| Framework | [Hono](https://hono.dev/) (TypeScript) |
| Database | Cloudflare D1 (SQLite) |
| Cache | Workers KV (3-tier: Memory → KV → D1) |
| Storage | Cloudflare R2 |
| Analytics | Cloudflare Analytics Engine (crawl stats & revenue tracking) |
| AI Paywall | [x402 protocol](https://x402.org) — USDC on Solana mainnet via [Coinbase CDP](https://docs.cdp.coinbase.com/) facilitator |

## Documentation

| Document | Description |
| --- | --- |
| [API Reference](docs/api.md) | Full endpoint documentation with examples |
| [Architecture](docs/architecture.md) | System design, caching strategy, and performance |
| [x402 AI Paywall](docs/x402-ai-paywall.md) | How AI crawlers are monetized via x402 |
| [Database Schema](docs/schema.md) | Table definitions and relationships |
| [Aliases & Collective Acts](docs/aliases-and-units.md) | How DJ aliases (`aka`) and duo/collective members are represented |

## API Overview

**Base URL**: `https://berghain.ravers.workers.dev`

All endpoints are free for humans. AI crawlers pay via the [x402 protocol](docs/x402-ai-paywall.md).

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
| `GET /api/artists/:id/performances` | Artist's full performance history |
| `GET /api/residents/current` | Current active resident DJs |
| `GET /api/shows` | Browse all events |
| `GET /api/export/artists` | Bulk export (all artists) |

See [docs/api.md](docs/api.md) for the complete API reference.

### Content Negotiation

All HTML pages support `Accept: text/markdown` for structured markdown responses optimized for AI/LLM consumption. LLM discovery files are available at [`/llms.txt`](https://berghain.ravers.workers.dev/llms.txt) and [`/llms-full.txt`](https://berghain.ravers.workers.dev/llms-full.txt).

## x402 AI Paywall

AI crawlers are detected by User-Agent and required to pay via the [x402 protocol](https://x402.org). Payments are processed in **USDC on Solana mainnet** through the [Coinbase Developer Platform (CDP)](https://docs.cdp.coinbase.com/) facilitator. Human users and search engine bots access everything for free.

```
Request → detectCrawler(User-Agent)
  ├── null (human / search bot) → free access
  └── "GPTBot" etc. (AI crawler) → HTTP 402 → pay via x402 → access granted
```

| Endpoint Type | Price |
| --- | --- |
| Statistics / Rankings | $0.01 |
| Artist details | $0.02 |
| Bulk exports | $0.10 |

80+ AI crawlers are detected including GPTBot, ChatGPT-Agent, ClaudeBot, Google-Agent, DeepSeekBot, GrokBot, Amazonbot, and more. All crawler activity and payment data is tracked via Cloudflare Analytics Engine and displayed on the [public transparency dashboard](https://berghain.ravers.workers.dev/dashboard/crawl-stats).

See [docs/x402-ai-paywall.md](docs/x402-ai-paywall.md) for details.

## Get Involved

This database is maintained by the community. Your contributions help preserve Techno history.

- [Report missing data](https://github.com/M-Igashi/berghain-database/issues/new?template=data_correction.md)
- [Report a bug](https://github.com/M-Igashi/berghain-database/issues/new?template=bug_report.md)
- [Request a feature](https://github.com/M-Igashi/berghain-database/issues/new?template=feature_request.md)

See [CONTRIBUTING.md](CONTRIBUTING.md) for detailed guidelines on submitting corrections with proper sources.

## Support

This database is a labor of love, maintained for the global Techno community.

| Bitcoin (BTC) | Solana (SOL) |
| --- | --- |
| `bc1qp4lg5mw0c9nv3ygjnnx5gg95wk7h6flpw5pvfq` | `8oj2PMky2Zx9qznjK5eG7AUdqRab8GnrFJ7UamfEKRu` |

## License

MIT License — see [LICENSE](LICENSE) for details.

---

*This is an unofficial community project. Not affiliated with Berghain or Ostgut Ton.*
