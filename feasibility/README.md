# Developer Feasibility Assessment

This section provides an honest, technical assessment of the MVP scope — what's straightforward, what's hard, and what risks we're managing.

---

## MVP Feasibility Summary

| Component | Effort | Risk | Notes |
| --- | --- | --- | --- |
| Public website (5–7 pages) | LOW | Low | Standard Next.js marketing site |
| Supabase Auth + Stripe billing | LOW-MEDIUM | Low | Well-documented integrations |
| User portal UI (dashboard, offer library, calculator, account, billing) | MEDIUM | Low | Moderately complex React UI |
| TheOddsAPI integration + data pipeline | MEDIUM | Low | Single API, clean docs, 1-min cron |
| Signup bonus conversion engine | MEDIUM | Medium | Math is simple; domain edge cases are the risk |
| Firecrawl scraping (4 sportsbooks) | MEDIUM-HIGH | Medium-High | Fragile by nature; manual fallback mitigates |
| AI instruction cards + validation + caching | MEDIUM | Medium | Prompt engineering + validation layer |
| Admin panel | LOW | Low | Standard CRUD |
| Email system | LOW | Low | Resend + React Email templates |
| Deployment + handover | LOW | Low | Standard Vercel workflow |

## Overall Verdict

**The MVP as scoped is feasible in 6–7 weeks.** The scope has been deliberately constrained to one API source, one calculation type (free bet conversion), 4 scraped sportsbooks, and no personalisation — this keeps the build focused and achievable.

The biggest risk is **scraper fragility** (sportsbook pages changing), which is mitigated by the manual admin entry fallback. The second biggest risk is **AI accuracy**, which is mitigated by the validation layer and deterministic template fallback.

Nothing in the MVP scope requires unproven technology or novel engineering. Every component uses established patterns with mature tooling.
