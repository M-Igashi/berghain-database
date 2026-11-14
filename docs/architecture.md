# Architecture

A single Cloudflare Worker serves the JSON API, every HTML page, and the Markdown versions of those pages. There is no origin server and no frontend framework.

**Contents**: [System Overview](#system-overview) · [Tech Stack](#tech-stack) · [Request Pipeline](#request-pipeline) · [Cache System](#cache-system) · [Database Read Budget](#database-read-budget) · [Frontend](#frontend) · [Content Negotiation](#content-negotiation) · [Operations](#operations) · [Security](#security) · [Performance Targets](#performance-targets)

## System Overview

```
Client (browser, API consumer, AI agent)
       │
       ▼
Cloudflare edge network
       │
       ▼
┌────────────────────────────────────────────────────────────────┐
│  Cloudflare Worker (TypeScript, Hono)                           │
│                                                                │
│  Middleware ──► Routes ──► JSON  /  HTML  /  Markdown           │
│                   │                                            │
│     ┌─────────────┼──────────────┬───────────────┐             │
│     ▼             ▼              ▼               ▼             │
│  Response      R2 bucket     Static Assets   Analytics Engine  │
│  cache         flyer PDFs,   font, icons,    AI crawler stats, │
│  memory        QR codes      share image     x402 revenue      │
│    → KV                                                        │
│    → D1                                                        │
└────────────────────────────────────────────────────────────────┘
       ▲                                  │
       │ Cron Trigger (daily warmup)       ▼
                              Coinbase CDP facilitator
                              (x402 payments, USDC on Solana)
```

## Tech Stack

### Platform

| Component | Technology | Purpose |
| --- | --- | --- |
| Runtime | Cloudflare Workers | Edge compute in V8 isolates |
| Language | TypeScript 7 | |
| Framework | Hono 4 | HTTP routing and middleware |
| Database | Cloudflare D1 (SQLite) | Source of truth: artists, events, performances, precomputed co-performers |
| Cache | In-memory `Map` and Workers KV | Response cache in front of D1 |
| Object storage | Cloudflare R2 | Flyer PDFs and QR codes |
| Static assets | Workers Static Assets | Font files, icons, and the share image |
| Scheduling | Cron Trigger | Daily cache warmup at 03:00 UTC |

### Application

| Component | Technology | Purpose |
| --- | --- | --- |
| Pages | Server-rendered HTML from TypeScript template functions | Shared layout, no client framework, no build step |
| Typography | Self-hosted JetBrains Mono | No third-party font requests |
| Markdown | A Markdown template per page | Served for `Accept: text/markdown` |
| API description | OpenAPI 3.1, `llms.txt`, `llms-full.txt` | Machine-readable discovery |
| Payments | x402 protocol (`@x402/hono`) with the Coinbase CDP facilitator | USDC micropayments on Solana mainnet for exports and flyer PDFs |
| Analytics | Cloudflare Analytics Engine, read through the Analytics SQL binding | AI crawler stats and revenue. Human visitors are not tracked |
| Search indexing | IndexNow, XML sitemap, RSS feed | |

### Tooling

| Component | Technology | Purpose |
| --- | --- | --- |
| Deployment | Wrangler | Manual deploys from a local checkout |
| Observability | Workers Logs and Traces | Structured logs and request traces |
| Security review | [Claude Code](https://claude.com/claude-code) (Claude Fable 5.2) | Reviews of code and configuration changes |

## Request Pipeline

Requests pass through the following middleware in order:

| # | Middleware | Applies to | What it does |
| --- | --- | --- | --- |
| 1 | ASN blocking | all | Rejects known abusive autonomous systems with `403` |
| 2 | Crawler stats | all | Detects AI crawlers by User-Agent and writes one event to Analytics Engine |
| 3 | CORS | all | Allows cross-origin requests from any origin |
| 4 | Citability and ETag | all | Adds canonical URL and data source headers. Adds an `ETag` to API and Markdown responses so `If-None-Match` revalidation returns an empty `304` |
| 5 | x402 paywall | `/api/export/*`, `/flyers/*` | Requires payment from **every** requester |
| 6 | x402 signal | `/api/*`, `/current-residents` | Free responses to AI crawlers carry x402 discovery headers, so agents can find the paid routes |
| 7 | Admin auth | `/admin/*` | Requires the admin key |

The paywall applies to exports and flyer PDFs regardless of who is asking. Every other route is free for everyone, including AI crawlers. See [x402 & AI Access](x402-ai-paywall.md).

## Cache System

### Tiers

| Tier | Storage | Scope | Lifetime |
| --- | --- | --- | --- |
| L1 | In-memory `Map` | One isolate | 2 hours to 30 days |
| L2 | Workers KV | Global | 1 to 30 days |
| L3 | Cloudflare D1 | Global | Permanent (source of truth) |

```
Request → L1 (memory) hit? → return
              │ miss
              ▼
         L2 (KV) hit? → fill L1, return
              │ miss
              ▼
         L3 (D1) query → fill L2 and L1, return
```

### Which data uses which tiers

Only data with a small, fixed number of cache keys is written to KV. Anything keyed by an artist, an event, or a search term stays in memory.

| Data | L1 (memory) | L2 (KV) |
| --- | --- | --- |
| Stats | 4 hours | 7 days |
| Rankings (all-time, per year, per floor) | 2 hours | 7 days |
| Event list | 6 hours | 7 days |
| Upcoming lineups | yes | 1 day |
| Full artist list | yes | yes |
| Current residents (API and page) | 30 days | 30 days |
| Years, period | 24 hours | 30 days |
| Sitemap, RSS | 6 to 12 hours | 7 days |
| Artist details, stats, and performances | 4 hours | not used |
| Artist and event pages | yes | not used |
| Batch performances (`/api/performances`) | not cached | not used |

Two rules follow from this:

- **Lists are cached whole and paged in code.** `limit`, `offset`, `page`, and `search` never become cache keys, and artist search filters the cached full list without touching D1.
- **High-cardinality paths skip KV.** Writing one KV key per artist would exhaust the daily KV write budget when a crawler sweeps all ~2,500 artists, so those paths go from memory straight to D1.

A daily Cron Trigger and every deploy warm the bounded keys, so the home page and its API calls are served from cache.

## Database Read Budget

D1 bills rows *scanned*, not rows returned, and a crawler sweep visits every URL once, so caching alone cannot protect the budget. The queries themselves are kept cheap:

- **Slug lookups use an index.** `/artists/:slug` resolves through an exact match on `normalized_name`. Unknown or partial slugs are matched against the cached artist list and never reach D1.
- **Co-performers are precomputed.** The "most shared bills" on an artist page come from the `artist_coperformers` table (top 5 per artist) instead of a self-join over all performances.
- **Counts are stored.** Per-artist totals live on the `artists` row and are recalculated after each import, so stats and rankings never count performances at request time.
- **Unknown input is answered without D1.** A year outside the covered range returns an empty list immediately.

## Frontend

- Every page is rendered on the server from a shared layout: design tokens, header, artist search, and footer are defined once.
- The home page is rendered from the same cached data the API serves (stats, upcoming lineups, residents, rankings, events). The year and floor filters fetch the public API.
- Bars are split by floor throughout the site: white for the Berghain floor, orange for Panorama Bar.
- Content pages make no third-party requests: the font is self-hosted and there are no analytics or tracking scripts. The one exception is the crawl stats dashboard, which loads Chart.js from a CDN.

## Content Negotiation

Content pages (`/`, `/current-residents`, `/artists/:slug`, `/shows/:event_id`) answer in two formats:

| `Accept` | Response |
| --- | --- |
| `text/html` (default) | Rendered HTML page |
| `text/markdown` | Structured Markdown for AI/LLM consumption |

Responses include a `Content-Signal` header indicating data usage permissions:

```
Content-Signal: ai-train=no, search=yes, ai-input=yes
```

## Operations

| Task | How |
| --- | --- |
| Deploy | Manual, through Wrangler. A deploy starts fresh isolates, which also resets every in-memory cache |
| Data update | Monthly import into D1, followed by a KV clear, a deploy, and a cache warmup |
| Search engines | Changed URLs are submitted through IndexNow after a data update |
| Monitoring | `/health`, Workers Logs and Traces, D1 query insights |

## Security

- **Secrets** (admin key, wallet address, payment facilitator credentials) are stored as Worker secrets and never in the repository.
- **Admin routes** require a key sent in a header, compared in constant time. The key is not accepted in the query string.
- **The paywall ignores User-Agent**, so it cannot be bypassed by pretending to be a browser.
- **No visitor data** is collected: no cookies, no accounts, no tracking scripts.
- **Security review**: code and configuration changes are reviewed with [Claude Code](https://claude.com/claude-code) running Claude Fable 5.2.

## Performance Targets

| Metric | Target |
| --- | --- |
| Response time | < 100 ms |
| Cache hit rate | > 85% |
| D1 rows read | < 1M per day |
| Error rate | < 0.1% |
