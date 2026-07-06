# Architecture

## System Overview

```
Client Request
       │
       ▼
Cloudflare Edge Network (300+ global PoPs)
       │
       ▼
┌──────────────────────────────────────────────────────────┐
│              Cloudflare Workers Runtime                   │
│                                                          │
│  ┌─────────────────────────────────────┐                 │
│  │         Hono Framework              │                 │
│  │       (TypeScript, Edge-native)     │                 │
│  └──────────┬──────────────────────────┘                 │
│             │                                            │
│  ┌──────────▼──────────┐  ┌─────────────┐  ┌──────────┐ │
│  │  3-Tier Cache       │  │ R2 Storage  │  │ Analytics│ │
│  │  L1: In-Memory      │  │ (Flyers,    │  │ Engine   │ │
│  │  L2: Workers KV     │  │  QR codes)  │  │ (Crawl   │ │
│  │  L3: D1 (SQLite)    │  └─────────────┘  │  Stats)  │ │
│  └─────────────────────┘                    └──────────┘ │
└──────────────────────────────────────────────────────────┘
```

## Components

| Component | Technology | Purpose |
| --- | --- | --- |
| Runtime | Cloudflare Workers | Edge compute (V8 isolates) |
| Framework | Hono | Lightweight HTTP router |
| Database | Cloudflare D1 | SQLite at the edge |
| Cache (L2) | Workers KV | Global key-value store |
| Storage | Cloudflare R2 | Static assets (flyers, images) |
| Analytics | Cloudflare Analytics Engine | AI crawler stats & revenue tracking |
| Payments | x402 protocol + Coinbase CDP | USDC micropayments on Solana mainnet (exports & flyer PDFs) |

## 3-Tier Cache System

The API implements a multi-layer caching strategy that reduces database reads by ~95%.

| Layer | Storage | Scope | TTL | Purpose |
| --- | --- | --- | --- | --- |
| L1 | In-memory Map | Per-isolate | 2–24 hours | Instant response for hot data |
| L2 | Workers KV | Global | 7–30 days | Cross-isolate shared cache |
| L3 | D1 Database | Global | Permanent | Source of truth |

### Cache TTL by Endpoint

| Data Type | L1 (Memory) | L2 (KV) |
| --- | --- | --- |
| Stats | 4 hours | 7 days |
| Rankings | 2 hours | 7 days |
| Events | 6 hours | 7 days |
| Artist details | 4 hours | 7 days |
| Current residents | 30 days | 30 days |
| Years / Period | 24 hours | 30 days |
| Search results | 2 hours | memory-only¹ |
| Sitemap / RSS | 6–12 hours | 7 days |

¹ High-cardinality endpoints (per-artist by id/slug/name, and free-text search) use a **memory → D1** tier only, skipping KV. Writing one KV key per artist would exceed the free-tier KV write limit when a crawler sweeps all ~2,500 artists, so these paths fall through to D1 (which has a much higher daily read budget) on a cold miss.

### Cache Flow

```
Request → L1 (memory) hit? → return
              │ miss
              ▼
         L2 (KV) hit? → populate L1, return
              │ miss
              ▼
         L3 (D1) query → populate L2 + L1, return
```

## Middleware Chain

Requests pass through the following middleware in order:

1. **ASN Blocking** — blocks known abusive autonomous systems
2. **Crawler Stats** — detects AI crawlers and writes events to Analytics Engine
3. **CORS** — enables cross-origin requests
4. **x402 Paywall** (`/api/export/*`, `/flyers/*`) — requires payment from **all** requesters
5. **x402 Signal** (`/api/*`, `/current-residents`) — free responses that carry x402 discovery headers so AI agents can find the paid routes

Note: the paywall applies to exports and flyer PDFs regardless of who is asking. Every other route is free for everyone, including AI crawlers.

## Performance Targets

| Metric | Target |
| --- | --- |
| Response time | < 100ms |
| Cache hit rate | > 85% |
| D1 reads | < 1M/day |
| Uptime | 99.9%+ |

## Content Negotiation

All HTML pages support content negotiation:

- `Accept: text/html` (default) — rendered HTML page
- `Accept: text/markdown` — structured markdown for AI/LLM consumption

Responses include a `Content-Signal` header indicating data usage permissions:

```
Content-Signal: ai-train=no, search=yes, ai-input=yes
```
