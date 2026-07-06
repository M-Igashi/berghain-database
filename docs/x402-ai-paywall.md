# x402 & AI Access

## Overview

The Berghain Database runs a **freemium** model. Almost the entire API is free for everyone — humans, search engines, **and AI crawlers** — which is deliberate: open access maximizes the archive's visibility in AI search and answer engines.

Only two categories of route are paid, via the [x402 protocol](https://x402.org), and they are paid by **every** requester (not just AI crawlers):

- **Bulk exports** — full-table dumps at `/api/export/*`
- **Historical flyer PDFs** — the original 2004–2009 flyer scans at `/flyers/*`

## How It Works

```
Request
  ├── /api/export/*, /flyers/*   → x402 payment middleware (all requests)
  │       ├── no payment  → HTTP 402 Payment Required (with payment instructions)
  │       └── valid X-PAYMENT → HTTP 200 (access granted)
  └── everything else            → free
          └── /api/*, /current-residents also return x402 *signal* headers
              so AI agents can discover the paid routes and pricing
```

AI crawlers are still **detected** by User-Agent — not to charge them for normal endpoints, but to (a) record activity for the public transparency dashboard, (b) attach x402 discovery headers to free responses, and (c) note when a crawler pays for an export or flyer.

## Pricing

| Endpoint | Price (USDC) | Applies to |
| --- | --- | --- |
| `GET /api/export/artists` | $0.10 | all requests |
| `GET /api/export/events` | $0.10 | all requests |
| `GET /api/export/performances` | $0.10 | all requests |
| `GET /flyers/*.pdf` | $0.01 | all requests |

Export endpoints support `?format=json` (default) or `?format=csv`.

## Free Endpoints (No Payment, Any Requester)

Everything not listed in the pricing table above is free, including:

- All HTML pages (`/`, `/current-residents`, `/artists/*`, `/shows/*`)
- `GET /api/stats`, `GET /api/stats/monthly` — statistics
- `GET /api/artists/ranking`, `GET /api/artists/ranking/year/:year` — rankings
- `GET /api/artists`, `GET /api/artists/:id`, `GET /api/artists/by-name/:name`
- `GET /api/artists/:id/performances`, `GET /api/artists/:id/stats`
- `GET /api/shows`, `GET /api/residents/current`, `GET /api/years`, `GET /api/period`
- `GET /api/flyers` — the flyer archive **index** (only the PDF files themselves are paid)
- `GET /llms.txt`, `GET /llms-full.txt`, `GET /openapi.json`
- `GET /.well-known/x402`, `GET /.well-known/agentic-capabilities.json`
- `GET /sitemap.xml`, `GET /robots.txt`, `GET /feed.xml`
- `GET /api/public/crawl-summary`, `GET /dashboard/crawl-stats`

## Payment Details

| Setting | Value |
| --- | --- |
| Protocol | [x402](https://x402.org) |
| Network | Solana mainnet |
| Currency | USDC (`EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v`) |
| Facilitator | [Coinbase CDP](https://docs.cdp.coinbase.com/) (`https://api.cdp.coinbase.com/platform/v2/x402`) |
| Wallet | `8oj2PMky2Zx9qznjK5eG7AUdqRab8GnrFJ7UamfEKRu` |
| Manifest | [`/.well-known/x402`](https://berghain.ravers.workers.dev/.well-known/x402) |

Payment verification and settlement are handled by the Coinbase Developer Platform (CDP) facilitator. The server generates ES256 JWTs to authenticate with the CDP API, which verifies on-chain USDC transactions on Solana mainnet.

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

1. Request a paid endpoint (e.g. `GET /api/export/artists`)
2. Receive `HTTP 402` with payment instructions
3. Submit a USDC transaction on Solana as specified
4. Retry the request with the payment proof in the `X-PAYMENT` header

## Detected AI Crawlers

Over 80 AI crawlers are recognized (for stats and x402 discovery signaling). A non-exhaustive list:

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
