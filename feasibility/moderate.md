# What's Moderately Hard

These are achievable but require careful implementation and testing. Moderate risk.

---

## Three API Integrations with Normalisation

You're pulling from **TheOddsAPI, Sportradar, and OddsJam** — three different schemas, three different auth methods, three different rate limit models, three different ways of identifying the same event.

### The Hard Part: Event Matching

When TheOddsAPI says "Lakers vs Celtics" and Sportradar says "Los Angeles Lakers at Boston Celtics" and OddsJam says "LAL @ BOS" — you need to reliably match these as the same event to compare odds across sources. There is no universal event ID across providers.

### API-Specific Challenges

**TheOddsAPI** — the easiest:
- Clean REST API, consistent naming
- Good documentation
- Straightforward rate limiting

**Sportradar** — complex:
- Nested XML/JSON responses with proprietary entity IDs
- Enterprise pricing (potentially expensive)
- Rich data but over-engineered for this use case

**OddsJam** — designed for their own UI:
- Data model built for OddsJam's interface, not external consumption
- May require significant transformation
- Documentation quality varies

### What Could Go Wrong

- Inconsistent event naming breaks matching logic on edge cases (postponed games, team name changes mid-season)
- Rate limits force tradeoffs between data freshness and cost
- API outages on one provider cascading into stale data if fallback logic isn't clean

### Recommendation

**Launch with TheOddsAPI only.** It covers 40+ US sportsbooks and is sufficient for MVP. Add Sportradar and OddsJam as post-launch enhancements when the matching logic has been validated against real data.

**Effort:** TheOddsAPI alone: 3–5 days. All three with normalisation: 10–14 days.

---

## 1-Minute Data Pipeline

Vercel Cron's minimum interval is 1 minute, but there are constraints:

### Timeout Risk

- Vercel serverless functions timeout at **60 seconds on Pro plan**
- If you're hitting 3 APIs, normalizing, running calculations, and writing to Supabase — 60 seconds might be tight
- If one API is slow or times out, the whole pipeline run could fail

### Idempotency

- If the cron fires twice or partially fails, you can't end up with duplicated or corrupted data
- Upserts (INSERT ON CONFLICT UPDATE) solve this for database writes
- But if the calculation step runs against incomplete data (only 1 of 3 APIs succeeded), opportunities will be wrong

### Mitigation: Split Pipeline

Break the pipeline into separate functions:

```
/api/cron/fetch-theoddsapi    → independent, 15-20s
/api/cron/fetch-sportradar    → independent, 15-20s
/api/cron/fetch-oddsjam       → independent, 15-20s
/api/cron/calculate           → runs after fetches, 20-30s
/api/cron/generate-cards      → async, only for new opportunities
```

Each function stays well within the 60-second timeout.

**Effort:** 3–5 days including error handling and monitoring.

---

## Arb / EV Calculation Engine

The math itself is simple (arb formula, free bet conversion, EV calculation). **The hard part is edge cases.**

### Domain Knowledge Required

- **Odds formats:** American, decimal, fractional — normalise everything
- **Vig/juice calculations** for accurate EV
- **Multi-leg promos:** "Bet $50 on a 3-leg parlay, get $200 in bonus bets" — much more complex math
- **Different free bet types:** Each has a different conversion formula:
  - Non-withdrawable free bets (don't return stake)
  - Bonus cash with rollover (must wager X times before withdrawal)
  - Odds boosts (percentage vs. fixed amount)
  - Deposit matches (with varying playthrough requirements)
  - Second-chance bets (conditional bonus based on loss)
- **Minimum odds requirements** that constrain which hedges are valid

### The Risk

If you don't deeply understand matched betting mechanics, you'll ship calculators that give wrong numbers. **Wrong numbers = users lose money = platform is dead.**

### Mitigation

- If you're not a matched bettor yourself, find one to QA every calculator and scenario
- Build comprehensive test suites with known correct outputs
- Start with the simplest type (free bet conversion) and validate before moving to others

**Effort:** 5–8 days for all calculation types with comprehensive testing.

---

## Azure Entra External ID + Stripe Tier Sync

### The Integration Challenge

You need a clean chain:
1. User signs up via Azure Entra → user record created in Supabase
2. Stripe customer created and linked to that user
3. Subscription changes → webhook → Supabase tier update
4. RLS policies read tier to gate access

### Why It's Harder Than It Looks

- Azure Entra External ID is relatively new (rebranded from Azure AD B2C)
- Documentation is improving but scattered across multiple Microsoft portals
- Connecting it cleanly with Stripe and Supabase requires custom middleware
- No off-the-shelf integration exists

### The Easier Alternative

**Supabase Auth** is built into the database layer:
- Native Google OAuth
- Email/password with email verification
- JWT tokens that RLS policies read automatically
- Stripe webhook → tier update is simpler because everything lives in Supabase

**Effort with Azure Entra:** 4–6 days. **Effort with Supabase Auth:** 2–3 days.
