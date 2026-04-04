# Architecture

## System Overview

```
Client Request
       │
       ▼
Cloudflare Edge Network (300+ global PoPs)
       │
       ▼
┌─────────────────────────────────────────────┐
│           Cloudflare Workers Runtime         │
│                                             │
│  ┌─────────────────────────────────────┐    │
│  │         Hono Framework              │    │
│  │       (TypeScript, Edge-native)     │    │
│  └──────────┬──────────────────────────┘    │
│             │                               │
│  ┌──────────▼──────────┐  ┌─────────────┐  │
│  │  3-Tier Cache       │  │ R2 Storage  │  │
│  │  L1: In-Memory      │  │ (Flyers,    │  │
│  │  L2: Workers KV     │  │  QR codes)  │  │
│  │  L3: D1 (SQLite)    │  └─────────────┘  │
│  └─────────────────────┘                    │
└─────────────────────────────────────────────┘
```

## Components

| Component | Technology | Purpose |
| --- | --- | --- |
| Runtime | Cloudflare Workers | Edge compute (V8 isolates) |
| Framework | Hono | Lightweight HTTP router |
| Database | Cloudflare D1 | SQLite at the edge |
| Cache (L2) | Workers KV | Global key-value store |
| Storage | Cloudflare R2 | Static assets (flyers, images) |
| AI Paywall | x402 protocol | USDC micropayments on Solana |

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
| Search results | 2 hours | 24 hours |
| Sitemap / RSS | 6–12 hours | 7 days |

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
2. **Crawler Stats** — detects and tracks AI crawler visits
3. **CORS** — enables cross-origin requests
4. **x402 Paywall** — charges AI crawlers via x402 (humans pass through)

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
