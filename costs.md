# Investment & Costs

## Development Cost

| Item | Cost |
| --- | --- |
| **MVP build (fixed price)** | AUD $15,000 |
| **Deposit to start** | AUD $3,000 |
| **Remaining balance** | AUD $12,000 (payment schedule confirmed at contract signing) |

This is a **fixed price**. No hourly overruns, no scope creep charges. If it's listed in the MVP Scope, it's included. Whitecrow carries the delivery risk.

---

## What the Client Pays For Post-Launch (Monthly)

Once the platform is live, the client is responsible for all third-party service subscriptions. These are paid directly to each provider — Whitecrow does not markup or resell.

| Service | What It Does | Low Estimate | High Estimate |
| --- | --- | --- | --- |
| **Vercel Pro** | Hosting, CDN, serverless functions, cron jobs | $20/month | $150/month |
| **Supabase Pro** | Managed PostgreSQL database | $25/month | $100/month |
| **TheOddsAPI** | Real-time odds from 40+ US sportsbooks | $150/month | $300/month |
| **LLM API (Claude or OpenAI)** | AI instruction card generation | $50/month | $200/month |
| **Firecrawl** | Sportsbook promo page scraping | $30/month | $100/month |
| **Upstash Redis** | Caching + rate limiting | $5/month | $20/month |
| **Resend** | Email delivery | $0/month | $20/month |
| **Sentry** | Error monitoring | $0/month | $30/month |
| **Stripe** | Payment processing | 2.9% + 30c/txn | — |
| **TOTAL (excl. Stripe)** | | **~AUD $300/month** | **~AUD $600/month** |

### What Drives Cost Up

- **TheOddsAPI** is the biggest line item. Cost scales with the number of API requests. More sports covered = more requests = higher cost. The $150–$300 range covers typical MVP usage.
- **LLM API** costs are kept flat by the Redis caching layer. Without caching, 1,000 users would cost ~$500/month. With caching, 1,000 users costs the same ~$50–$80/month because the same opportunity generates the same card for everyone.
- **Vercel** scales with traffic. At MVP volumes (hundreds of users), $20–$40/month is typical. At scale (tens of thousands), it can reach $150+.

### What the Client Does NOT Need to Pay For

| Service | Why Not |
| --- | --- |
| **Sportradar** | Not used in MVP — TheOddsAPI is sufficient |
| **OddsJam** | Not used in MVP — TheOddsAPI is sufficient |
| **Any server/VPS/cloud hosting** | Everything runs on serverless — no servers to manage |
| **Database administrator** | Supabase is fully managed |
| **DevOps tooling** | Vercel handles CI/CD automatically |

---

## Break-Even Analysis

Assuming Basic tier at AUD $49/month and monthly opex of AUD $450/month (mid-range):

| Monthly Opex | Subscribers to Break Even |
| --- | --- |
| AUD $300 (low) | **7 subscribers** |
| AUD $450 (mid) | **10 subscribers** |
| AUD $600 (high) | **13 subscribers** |

At 50 subscribers on the Basic plan: **AUD $2,450/month revenue** minus ~$500/month costs = **~AUD $1,950/month profit**.

At 200 subscribers: **AUD $9,800/month revenue** minus ~$800/month costs = **~AUD $9,000/month profit**.

The unit economics are strong. Costs scale slowly (caching keeps AI flat, single API poll serves all users), while revenue scales linearly with subscribers.

---

## One-Time Costs (Client's Responsibility, Not Included in Build)

| Item | Estimated Cost | Notes |
| --- | --- | --- |
| **Domain name** | $15–$30/year | Client purchases their own domain |
| **US gambling law review** | $3,000–$8,000 one-time | Recommended before launch — covers scraping legality + state advertising compliance |
| **Copywriting** (if needed) | $1,000–$3,000 one-time | If client needs professional help writing website copy |
| **Brand design** (if needed) | $500–$2,000 one-time | If client doesn't have logo/brand assets |
