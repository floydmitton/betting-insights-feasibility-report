# Executive Summary

## US Sports Betting Insights Platform — Full Feasibility Report

**Prepared for:** Alex Dowling\
**Prepared by:** Whitecrow Development Team\
**Date:** March 2026

---

## What Is This Platform?

A US-based subscription SaaS platform targeting the **sports betting bonus harvesting and matched betting market**. The platform aggregates real-time odds and promotional data from US sportsbooks, applies AI-driven analysis to surface high-value matched betting and bonus conversion opportunities, and delivers curated insights to subscribers via a secure, tiered-access dashboard.

**This is not a tipster or bet-picking platform.** It does not predict winners. Instead, it finds mathematically guaranteed (or near-guaranteed) profit opportunities that arise from sportsbook sign-up bonuses, free bets, deposit matches, and odds boosts.

## Key Numbers

| Metric | Value |
| --- | --- |
| **Fixed Build Price** | AUD $15,000 |
| **Estimated Timeline** | 5–7 weeks (realistic: 8–10 weeks) |
| **Monthly Operating Costs** | AUD $285–$890/month at MVP scale |
| **Target Market** | 38 US legal sports betting states |
| **Revenue Model** | Tiered SaaS subscriptions (Free / Basic / Premium) |
| **Comparable Products** | OddsJam, DarkHorse Odds, Pikkit, BonusFinder |

## The Core Loop

1. **Ingest odds** — Pull real-time odds from every US sportsbook via API every ~1 minute
2. **Ingest promos** — Scrape/catalog what bonuses each sportsbook is currently offering in each state
3. **Find opportunities** — For each bonus, the calculation engine finds the optimal hedge bet that converts the bonus into real cash with minimal risk
4. **Generate instructions** — AI writes plain-English step-by-step instructions telling the user exactly what to do
5. **Serve to users** — Filtered by their state, their sportsbook accounts, and their subscription tier

## Concrete Example

> DraftKings is offering: **"Sign up, bet $5, get $200 in bonus bets"** in New York.
>
> The system:
> 1. Knows this promo exists (Offer Library)
> 2. Finds that the Lakers are +130 on DraftKings and the Celtics are -125 on FanDuel
> 3. Calculates: place the $200 free bet on Lakers at +130 on DraftKings, hedge with $148 on Celtics at -125 on FanDuel
> 4. Result: no matter who wins, the user walks away with ~$112 profit
> 5. AI generates a card: "Here's exactly what to do, step by step..."

The user does the actual betting. The platform just tells them what to do.

## Overall Feasibility Verdict

**This is a feasible project.** The tech stack is modern and appropriate, the architecture is well-designed, and the market is validated by existing competitors. The biggest risks are:

- **Offer Library scraping** — fragile, high maintenance, legally grey
- **Three-API event matching** — unglamorous plumbing that eats weeks
- **AI accuracy validation** — the gap between "nice-sounding" and "correct, actionable" is wider than it looks
- **Timeline** — 5–7 weeks is aggressive; 8–10 weeks is realistic

Full analysis follows in this report.
