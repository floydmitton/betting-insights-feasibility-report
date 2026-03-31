# Risks & Mitigations

Every risk in the MVP build, how likely it is, and exactly how we handle it.

---

## Technical Risks

| # | Risk | Severity | Likelihood | Mitigation |
| --- | --- | --- | --- | --- |
| T1 | **Scraper breaks after launch** due to sportsbook page redesign | Medium | HIGH (will happen) | Manual admin entry is a first-class feature. Admin health dashboard flags broken scrapers immediately. |
| T2 | **AI hallucinates** incorrect stake amounts or book names | High | Medium | Validation layer checks every AI output against source data. Falls back to deterministic template if validation fails. |
| T3 | **Vercel cron timeout** — pipeline function exceeds 60 seconds | Medium | Low | Pipeline split into parallel functions per sport group, each well within timeout. |
| T4 | **TheOddsAPI rate limit** exceeded or API goes down | Medium | Low | Pipeline has error handling and graceful degradation. Dashboard serves cached data. Stale data is flagged, not served as fresh. |
| T5 | **Odds move between calculation and user action** | Medium | HIGH (inherent to the product) | Every opportunity card shows a timestamp. Users are warned that odds may have moved. Real-time validation is Phase 2+. |
| T6 | **Free bet conversion formula error** in an edge case | High | Low | Comprehensive test suite with 20+ manually verified scenarios. Domain expert QA recommended before launch. |

## Business / Operational Risks

| # | Risk | Severity | Likelihood | Mitigation |
| --- | --- | --- | --- | --- |
| B1 | **Sportsbook scraping legality** — books may send C&D | Medium | Low-Medium | Legal review recommended pre-launch. Manual entry fallback means scraping is a convenience, not a dependency. |
| B2 | **Client provides copy/assets late** — delays design and public site | Medium | Medium | We provide a copy brief and deadlines in Week 1. Clear in the Responsibilities Matrix that delays are on the client. |
| B3 | **Design approval takes too long** — delays portal development | High | Medium | Designs submitted early in Week 2. Review call scheduled. Clear SLA: feedback within 2 business days. |
| B4 | **Scope creep during build** — client requests features not in MVP | Medium | Medium | MVP scope is exhaustively documented. Any additions are quoted separately before work begins. |
| B5 | **Sportsbooks restrict matched bettors** — users get account limited | Medium | HIGH (industry-wide) | This is a market risk, not a tech risk. Should be disclosed to users in ToS and FAQ. Not our responsibility, but we should set expectations. |

## What We're NOT Worried About

| Non-Risk | Why |
| --- | --- |
| **Auth security** | Delegated to Supabase Auth — battle-tested, no custom auth code |
| **Payment security** | Delegated to Stripe — PCI compliance is Stripe's responsibility |
| **Database access control** | Row Level Security at Supabase enforces tier gating at the database layer |
| **Scalability at MVP** | Serverless architecture auto-scales. This doesn't need to handle 100k users on day 1. |
| **Single API integration** | TheOddsAPI is well-documented with stable endpoints. No event matching complexity (that's Phase 2+). |

## Risk Ownership

| Category | Owner |
| --- | --- |
| Technical bugs in delivered code | **Whitecrow** (covered by 30-day warranty) |
| Scraper maintenance after launch | **Client** (or separate Whitecrow retainer) |
| Third-party API outages (TheOddsAPI, Stripe, Supabase) | **Client** (we build error handling, but can't control uptime of external services) |
| Legal compliance (gambling regs, advertising, scraping) | **Client** (we recommend legal review; we don't provide legal advice) |
| Content creation (copy, blog posts) | **Client** |
| Marketing and user acquisition | **Client** |
| Monthly service costs | **Client** (paid directly to each provider) |
