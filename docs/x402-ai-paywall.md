# x402 AI Paywall

## Overview

The Berghain Database uses the [x402 protocol](https://x402.org) to monetize AI crawler access. Human users and search engine bots access everything for free — only AI crawlers are required to pay.

## How It Works

```
Request → detectCrawler(User-Agent)
  ├── null (human / search bot) → free access
  └── AI crawler detected → x402 payment middleware
        ├── No payment → HTTP 402 with payment instructions
        └── Valid payment → HTTP 200 (access granted)
```

1. The middleware inspects the `User-Agent` header on every `/api/*` request
2. If the User-Agent matches a known AI crawler, the x402 payment middleware activates
3. The server returns `HTTP 402 Payment Required` with payment instructions in the response headers
4. AI agents with x402 support pay automatically — no accounts or API keys needed
5. After payment verification, the response is returned normally

## Pricing

| Endpoint | Price (USDC) | Description |
| --- | --- | --- |
| `GET /api/stats` | $0.01 | Overall statistics |
| `GET /api/stats/monthly` | $0.01 | Monthly breakdown |
| `GET /api/artists/ranking` | $0.01 | Artist rankings |
| `GET /api/artists/ranking/year/:year` | $0.01 | Yearly rankings |
| `GET /api/artists/:id/performances` | $0.02 | Performance history |
| `GET /api/artists/:id/stats` | $0.02 | Artist statistics |
| `GET /api/export/artists` | $0.10 | Bulk artist export |
| `GET /api/export/events` | $0.10 | Bulk event export |
| `GET /api/export/performances` | $0.10 | Bulk performance export |

## Free Endpoints (No Payment Required)

These endpoints are always free, regardless of User-Agent:

- All HTML pages (`/`, `/current-residents`, `/artists/*`, `/shows/*`)
- `GET /api/artists` — artist search
- `GET /api/artists/:id` — basic artist info
- `GET /api/shows` — event list
- `GET /api/residents/current` — current residents
- `GET /api/years`, `GET /api/period`, `GET /api/flyers`
- `GET /llms.txt`, `GET /llms-full.txt`
- `GET /sitemap.xml`, `GET /robots.txt`, `GET /feed.xml`
- `GET /api/public/crawl-summary` — public crawl stats
- `GET /dashboard/crawl-stats` — crawl stats dashboard

## Payment Details

| Setting | Value |
| --- | --- |
| Protocol | [x402](https://x402.org) |
| Network | Solana |
| Currency | USDC |
| Facilitator | `https://x402.org/facilitator` |
| Wallet | `8oj2PMky2Zx9qznjK5eG7AUdqRab8GnrFJ7UamfEKRu` |

## Using x402 as a Client

### With `@x402/fetch` (JavaScript/TypeScript)

```javascript
import { wrapFetchWithPayment } from "@x402/fetch";

const x402Fetch = wrapFetchWithPayment(fetch, wallet);
const response = await x402Fetch(
  "https://berghain.ravers.workers.dev/api/stats"
);
const data = await response.json();
```

### Manual Flow

1. Request a paid endpoint
2. Receive `HTTP 402` with `X-PAYMENT` header containing JSON payment instructions
3. Submit a USDC transaction on Solana as specified
4. Retry the request with the payment proof in the `X-PAYMENT-RESPONSE` header

## Detected AI Crawlers

The following AI crawlers are detected and subject to x402 pricing:

**OpenAI / Microsoft**: GPTBot, ChatGPT-User, OAI-SearchBot

**Anthropic**: ClaudeBot, Claude-Web, anthropic-ai

**Google**: Google-Extended, GoogleOther, GoogleOther-Image, Google-CloudVertexBot

**Meta**: Meta-ExternalAgent, FacebookBot

**Other Major**: Amazonbot, Applebot-Extended, PerplexityBot, Bytespider (TikTok), Cloudflare-AI-Search

**Data / Research**: CCBot (Common Crawl), Diffbot, cohere-ai, YouBot, AI2Bot, webz.io, Timpibot, ImagesiftBot

**Chinese Search / AI**: Sogou, Baidu-Spider-AI, TencentBot, 360Spider, YisouSpider

> Human browsers and traditional search engine crawlers (Googlebot, bingbot, etc.) are **not** affected. They access all content for free.

## Crawl Statistics

### Public API

`GET /api/public/crawl-summary?days=N` returns aggregated crawler statistics (max 90 days). No authentication required.

### Dashboard

A visual dashboard is available at [`/dashboard/crawl-stats`](https://berghain.ravers.workers.dev/dashboard/crawl-stats) showing:

- Daily crawler request volume (paid vs. blocked)
- Crawler rankings by request count
- Most accessed paths
- 30-day trend charts
