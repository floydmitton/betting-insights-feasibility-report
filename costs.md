# Investment & Costs

## Upfront Investment

| Item | Cost |
| --- | --- |
| **Full MVP Build (Fixed Price)** | AUD $15,000 |
| **Deposit to Start** | AUD $3,000 |
| **Remaining Balance** | AUD $12,000 (payment schedule TBD) |

The fixed price covers everything in the MVP scope (Section 3 of the original proposal). No hourly overruns, no scope-creep surprises. Whitecrow carries the delivery risk.

---

## Monthly Operating Costs (Post-Launch)

These are ongoing costs the client bears after the platform is live.

### Proposed Estimates

| Service | Low / month | High / month | Notes |
| --- | --- | --- | --- |
| Vercel Pro (hosting + CDN + serverless) | AUD $20 | AUD $150 | Auto-scales with traffic |
| Supabase Pro (managed database) | AUD $25 | AUD $100 | Managed Postgres + real-time |
| TheOddsAPI (primary odds data feed) | AUD $150 | AUD $300 | 40+ US sportsbooks |
| LLM API — Claude or OpenAI (AI engine) | AUD $50 | AUD $200 | Cached outputs reduce cost |
| Azure Entra External ID (auth) | AUD $0 | AUD $30 | Free to 50,000 MAU |
| Upstash Redis + Resend + Sentry | AUD $15 | AUD $60 | Cache, email, monitoring |
| Stripe processing fees | 2.9% + 30c per txn | — | Scales with revenue |
| **TOTAL** | **~AUD $285** | **~AUD $890** | |

### Costs Not Reflected in the Proposal

| Service | Estimated Cost | Notes |
| --- | --- | --- |
| **Sportradar API** | AUD $500–$2,000+/month | Enterprise pricing; clarify before committing |
| **OddsJam API** | AUD $150–$300/month | Separate subscription required |
| **Firecrawl** | AUD $30–$100/month | Scraping service pricing |
| **Domain + DNS** | AUD $15–$30/year | Negligible |
| **Legal review** (one-time) | AUD $3,000–$8,000 | US gambling law specialist |

### Realistic Monthly Costs

| Scenario | Monthly Cost |
| --- | --- |
| **MVP launch (1 API, minimal AI usage)** | AUD $300–$500/month |
| **Growing (3 APIs, moderate traffic)** | AUD $800–$1,500/month |
| **Scale (10,000+ users, all features)** | AUD $1,500–$3,000/month |

### Break-Even Analysis

Assuming Basic plan at $49/month (AUD ~$75):

| Monthly Opex | Subscribers to Break Even |
| --- | --- |
| AUD $500 | ~7 subscribers |
| AUD $1,000 | ~14 subscribers |
| AUD $1,500 | ~20 subscribers |

The unit economics are strong. Even at the high end of costs, break-even is achievable with a small subscriber base.

---

## Cost Optimisation Strategies

1. **Start with one API** (TheOddsAPI) — saves $650–$2,300/month on Sportradar + OddsJam
2. **Use Supabase Auth** instead of Azure Entra — saves complexity, functionally equivalent
3. **Aggressive AI caching** — keeps LLM costs flat regardless of user count
4. **Manual Offer Library at launch** — avoids Firecrawl cost and maintenance burden
5. **Annual API subscriptions** — most providers offer discounts for annual commitment
