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
| Network | Solana mainnet |
| Currency | USDC (`EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v`) |
| Facilitator | [Coinbase CDP](https://docs.cdp.coinbase.com/) (`https://api.cdp.coinbase.com/platform/v2/x402`) |
| Wallet | `8oj2PMky2Zx9qznjK5eG7AUdqRab8GnrFJ7UamfEKRu` |

Payment verification and settlement are handled by the Coinbase Developer Platform (CDP) facilitator. The server generates ES256 JWTs for authenticating with the CDP API, which verifies on-chain USDC transactions on Solana mainnet.

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

Over 80 AI crawlers are detected and subject to x402 pricing:

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

> Human browsers and traditional search engine crawlers (Googlebot, bingbot, etc.) are **not** affected. They access all content for free.

## Crawl Statistics & Revenue Tracking

All AI crawler activity is tracked via **Cloudflare Analytics Engine**, providing real-time analytics without impacting request performance.

Each crawl event records:
- Crawler identity (User-Agent)
- Requested path
- Event type (request or paid)
- Payment amount (for successful x402 transactions)

### Public API

`GET /api/public/crawl-summary?days=N` returns aggregated crawler statistics including per-crawler revenue (max 90 days). No authentication required.

### Dashboard

A visual dashboard is available at [`/dashboard/crawl-stats`](https://berghain.ravers.workers.dev/dashboard/crawl-stats) showing:

- Daily crawler request volume (paid vs. blocked)
- Daily revenue chart (USDC)
- Per-crawler revenue breakdown
- Crawler rankings by request count and payment status
- Most accessed paths
- 30/90-day trend views
