# x402 & AI Access

## Overview

The Berghain Database runs a **freemium** model. Almost the entire API is free for everyone (humans, search engines, **and AI crawlers**), which is deliberate: open access maximizes the archive's visibility in AI search and answer engines.

Only one thing is paid, via the [x402 protocol](https://x402.org), and it is paid by **every** requester (not just AI crawlers): **bulk exports**, the full-table dumps at `/api/export/*`.

## How It Works

```
Request
  ├── /api/export/*    → x402 payment middleware (all requests)
  │       ├── no payment  → HTTP 402, requirements in the PAYMENT-REQUIRED header
  │       └── valid PAYMENT-SIGNATURE → HTTP 200 (access granted)
  └── everything else  → free
```

AI crawlers are still **detected** by User-Agent. This is not to charge them for normal endpoints, but to record activity for the public transparency dashboard and to note when a crawler pays. The paywall itself ignores User-Agent.

## Pricing

| Endpoint | Price (USDC) | Applies to |
| --- | --- | --- |
| `GET /api/export/artists` | $0.10 | all requests |
| `GET /api/export/events` | $0.10 | all requests |
| `GET /api/export/performances` | $0.10 | all requests |

Export endpoints support `?format=json` (default) or `?format=csv`.

### Flyer PDFs are not sold

The 2004–2009 monthly flyers are Berghain's own work. Until November 2026 this site sold copies for $0.01 each. It no longer hosts them: `GET /api/flyers` lists each flyer with a `source_url` pointing to the official PDF on berghain.berlin, and `/flyers/:filename` redirects there.

## Free Endpoints (No Payment, Any Requester)

Everything not listed in the pricing table above is free, including:

- All HTML pages (`/`, `/current-residents`, `/artists/*`, `/shows/*`)
- `GET /api/stats`, `GET /api/stats/monthly`
- `GET /api/artists/ranking`, `GET /api/artists/ranking/year/:year`
- `GET /api/artists`, `GET /api/artists/:id`, `GET /api/artists/by-name/:name`
- `GET /api/artists/:id/performances`, `GET /api/artists/:id/stats`
- `GET /api/performances` (up to 200 artist ids per request)
- `GET /api/shows`, `GET /api/shows/upcoming`, `GET /api/residents/current`, `GET /api/years`, `GET /api/period`
- `GET /api/flyers`, `GET /flyers/:filename` (redirect to the official PDF)
- `GET /llms.txt`, `GET /llms-full.txt`, `GET /openapi.json`
- `GET /.well-known/x402`, `GET /.well-known/agentic-capabilities.json`
- `GET /sitemap.xml`, `GET /robots.txt`, `GET /feed.xml`
- `GET /api/public/crawl-summary`, `GET /dashboard/crawl-stats`

## Payment Details

| Setting | Value |
| --- | --- |
| Protocol | [x402](https://x402.org), version 2 |
| Scheme | `exact` (fixed price per request) |
| Currency | USDC |
| Facilitator | [Coinbase CDP](https://docs.cdp.coinbase.com/) (`https://api.cdp.coinbase.com/platform/v2/x402`) |
| Manifest | [`/.well-known/x402`](https://berghain.ravers.workers.dev/.well-known/x402) |

You can pay on either network:

| Network | Id | USDC | Recipient |
| --- | --- | --- | --- |
| Solana mainnet | `solana:5eykt4UsFv8P8NJdTREpY1vzqKqZKvdp` | `EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v` | `8oj2PMky2Zx9qznjK5eG7AUdqRab8GnrFJ7UamfEKRu` |
| Base mainnet | `eip155:8453` | `0x833589fCD6eDb6E08f4c7C32D4f71b54bdA02913` | `0x82E31717373846324B663E0EaF040fA8553088A1` |

The 402 response lists both in its `accepts` array. Always take the amount and recipient from that response rather than from this table.

Payment verification and settlement are handled by the Coinbase Developer Platform (CDP) facilitator.

### Discovery metadata

Each paid route describes itself for x402 marketplaces (the Bazaar extension): a service name, tags, the query parameters it takes, an example of the response, and a short "use when" description. Agents that search a marketplace can decide whether a route fits before paying.

## Using x402 as a Client

### With `@x402/fetch` (JavaScript/TypeScript)

```javascript
import { wrapFetchWithPayment } from "@x402/fetch";

const x402Fetch = wrapFetchWithPayment(fetch, wallet);
const response = await x402Fetch(
  "https://berghain.ravers.workers.dev/api/export/artists?format=csv"
);
```

### Manual Flow

1. Request a paid endpoint (for example `GET /api/export/artists`)
2. Receive `HTTP 402`. The `PAYMENT-REQUIRED` header holds the payment requirements as base64-encoded JSON
3. Sign a USDC payment for one of the networks in `accepts`
4. Retry the request with the signed payment in the `PAYMENT-SIGNATURE` header

## Detected AI Crawlers

Over 80 AI crawlers are recognized (for the public statistics). A non-exhaustive list:

**OpenAI / Microsoft**: GPTBot, ChatGPT-User, ChatGPT-Agent, OAI-SearchBot, Operator

**Anthropic**: ClaudeBot, Claude-Web, Claude-SearchBot, Claude-User, anthropic-ai

**Google**: Google-Extended, GoogleOther, GoogleOther-Image, Google-CloudVertexBot, Google-Agent, Gemini-Deep-Research, Google-NotebookLM

**Amazon / AWS**: Amazonbot, bedrockbot, NovaAct

**Meta**: Meta-ExternalAgent, FacebookBot, Manus-User

**Apple**: Applebot-Extended

**xAI**: GrokBot

**Mistral**: MistralAI-User

**DeepSeek**: DeepSeekBot

**Perplexity**: PerplexityBot, Perplexity-User

**Cloudflare**: Cloudflare-AI-Search, Cloudflare-AutoRAG

**AI Search**: DuckAssistBot, Bravebot, PhindBot, ExaBot, TavilyBot, iaskspider, Andibot, kagi-fetcher, LinerBot, Anomura

**AI Agents / Tools**: Devin, FirecrawlAgent, Crawl4AI, ApifyBot

**ByteDance**: Bytespider, TikTokSpider

**Chinese AI**: ChatGLM-Spider, Sogou, Baidu-Spider-AI, TencentBot, 360Spider, YisouSpider

**Huawei**: PetalBot

**Korean / Japanese AI**: WRTNBot, SBIntuitionsBot

**Data / Training**: CCBot (Common Crawl), Diffbot, cohere-ai, webz.io, Brightbot

**Others**: YouBot, AI2Bot, Timpibot, ImagesiftBot, QualifiedBot, KlaviyoAIBot

> Human browsers and traditional search engine crawlers (Googlebot, bingbot, etc.) are unaffected and, like AI crawlers, access all non-paid content for free.

## Crawl Statistics & Revenue Tracking

All AI crawler activity — free requests and paid transactions — is tracked via **Cloudflare Analytics Engine**, providing real-time analytics without impacting request performance.

Each event records:
- Crawler identity (User-Agent)
- Requested path
- Event type (request or paid)
- Payment amount (for successful x402 transactions)

### Public API

`GET /api/public/crawl-summary?days=N` returns aggregated crawler statistics including per-crawler revenue (max 90 days). No authentication required.

### Dashboard

A visual dashboard is available at [`/dashboard/crawl-stats`](https://berghain.ravers.workers.dev/dashboard/crawl-stats) showing daily crawler volume (paid vs. free), revenue, per-crawler breakdowns, and most-accessed paths.
