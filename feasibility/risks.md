# What May Not Be Doable In Scope

These items are specified in the proposal but may need to be descoped or deferred based on practical constraints.

---

## Risk Matrix

| Item | Proposed | Risk | Realistic Outcome |
| --- | --- | --- | --- |
| Reliable scraping of 10+ sportsbooks at launch | Full automated Offer Library | HIGH | Launch with 3–4 books + manual entry |
| All 3 API integrations normalised in Week 3 | Single sprint | HIGH | Launch with TheOddsAPI only; add others post-launch |
| 1-minute refresh that's truly reliable | Vercel Cron + serverless | MEDIUM | Acceptable for MVP; not production-grade real-time |
| Full admin panel in same sprint as entire portal | Week 4 | MEDIUM | Admin panel gets minimal version; enhanced post-launch |
| Multi-leg / parlay promo calculations | Implied in calculator tools | HIGH | Defer to post-MVP; focus on single-bet promos |

---

## Detailed Risk Analysis

### Scraping Reliability at Scale

**Proposed:** Firecrawl scrapes all major sportsbook promo pages automatically.

**Reality:** Each sportsbook is a separate engineering problem. DraftKings promos are structured differently from FanDuel, which is different from BetMGM, which is different from Caesars. Each requires custom extraction logic, and each will break when the sportsbook redesigns (which happens frequently).

**Recommendation:** Descope to manual entry with automated scraping as a bonus. Budget ongoing developer time for scraper maintenance.

### Three APIs in One Sprint

**Proposed:** Week 3 delivers all three API integrations (TheOddsAPI, Sportradar, OddsJam) normalised and flowing.

**Reality:** Each integration is 2–4 days of work. Event matching across all three is an additional 3–5 days. That's 9–17 days of work in a 5-day sprint.

**Recommendation:** Ship with TheOddsAPI only. It covers 40+ US sportsbooks and is sufficient for a compelling MVP. Add Sportradar and OddsJam iteratively.

### Vercel Cron Reliability

**Proposed:** 1-minute data pipeline via Vercel Cron.

**Reality:** Vercel Cron is reliable for most cases, but:
- No guaranteed exactly-once execution
- Function timeout at 60 seconds limits pipeline complexity
- No built-in retry on failure
- No built-in dead-letter queue for failed executions

**For MVP:** Acceptable. The platform serves cached data, so a missed pipeline run means data is 2 minutes stale instead of 1. Users won't notice.

**For scale:** May need to migrate to a dedicated job scheduler (e.g., Inngest, Trigger.dev, or a separate worker service).

### Admin Panel Scope

**Proposed:** Full admin panel with user management, subscription visibility, manual offer entry, and platform metrics — built in the same week as the entire user portal.

**Reality:** Something will get cut. The portal has 6–10 pages of authenticated UI, the AI integration, and the dashboard. Adding a full admin panel in the same sprint is unrealistic.

**Recommendation:** Build a minimal admin panel (user list + manual offer entry) and enhance it post-launch.

### Multi-Leg Promo Calculations

**Proposed:** Implied in the scope under "the specific bonus types covered will be confirmed at kickoff."

**Reality:** Multi-leg (parlay) promotions are exponentially more complex to calculate:
- A 3-leg parlay has hundreds of possible outcome combinations
- Hedging a parlay requires multiple individual bets on opposing outcomes
- The calculation engine needs to evaluate all permutations to find optimal hedges
- Most multi-leg promos have minimum odds per leg, further constraining the search space

**Recommendation:** Explicitly exclude multi-leg/parlay calculations from MVP. Focus on single-event opportunities (moneyline, spread, total). Multi-leg support is a genuine Phase 2 feature.

---

## Timeline Comparison

| Phase | Proposed | Realistic (All Features) | Realistic (Descoped MVP) |
| --- | --- | --- | --- |
| Architecture + setup | 1 week | 1 week | 1 week |
| Design + auth + billing | 1 week | 1.5–2 weeks | 1.5 weeks |
| Data pipeline + APIs | 1 week | 2–2.5 weeks | 1 week (1 API) |
| Portal + AI + admin | 1 week | 2–3 weeks | 2 weeks |
| QA + launch | 1–2 weeks | 1–2 weeks | 1 week |
| **Total** | **5–7 weeks** | **8–10 weeks** | **6–7 weeks** |

## Key Takeaway

The proposal scope is achievable, but not in the proposed timeline with all features at full fidelity. The choice is:

1. **Full scope, longer timeline** (8–10 weeks)
2. **Descoped MVP, original timeline** (6–7 weeks) — recommended
3. **Full scope, original timeline** — high risk of quality issues and tech debt
