# Executive Summary

## US Sports Betting Insights Platform

**Prepared by:** Whitecrow Development\
**Prepared for:** \[Client Name]\
**Date:** March 2026\
**Document type:** MVP Development Proposal & Feasibility Report

---

## What We Are Building

A subscription-based web platform that helps US sports bettors **convert sportsbook sign-up bonuses into guaranteed cash**. The platform pulls real-time odds data, identifies the mathematically optimal hedge bets for each bonus, and delivers plain-English instructions telling users exactly what to bet, where, and for how much.

**This is not a tipster platform.** It does not predict winners. It uses pure mathematics to extract guaranteed or near-guaranteed value from sportsbook promotions.

## What's Included in the MVP (AUD $15,000)

For the fixed development price, we deliver a working, launched product that:

| We Build | Detail |
| --- | --- |
| **Odds data pipeline** | Real-time odds from TheOddsAPI (~40 US sportsbooks), refreshed every ~1 minute |
| **Sportsbook promo scraping** | Automated scraping of **4 major sportsbooks** (DraftKings, FanDuel, BetMGM, Caesars) for active sign-up bonus offers |
| **Signup bonus conversion engine** | Calculation engine that finds the optimal hedge bet to convert each sign-up free bet into cash |
| **AI instruction cards** | Plain-English, step-by-step instructions for every opportunity — powered by LLM with validation and caching |
| **3-tier subscription system** | Free / Basic / Premium tiers with Stripe billing, webhook-driven access control, and database-level gating |
| **Public marketing website** | 5–7 SEO-optimised pages (Home, How It Works, Pricing, FAQ, Blog template, Contact) |
| **User portal** | Authenticated dashboard, opportunity feed, offer library, free bet calculator, account management |
| **Admin panel** | Internal tool for user management, manual offer entry/override, and platform health monitoring |
| **Email system** | Welcome, billing, and daily digest emails via Resend |
| **Production deployment** | Live on Vercel with custom domain, SSL, monitoring, and full credential handover |

## What's NOT in the MVP (Phase 2+)

| Feature | Why It's Phase 2 |
| --- | --- |
| Additional odds APIs (Sportradar, OddsJam) | Not needed for launch — TheOddsAPI covers 40+ books |
| Scraping beyond 4 sportsbooks | Diminishing returns; each book is custom work |
| Personalised bonus conversion (user's specific books/state auto-filters) | Requires onboarding flow + profile-based filtering logic |
| Deposit match, odds boost, second chance bet calculations | Each is a separate calculation type with different math |
| Real-time high-value alerts (push/email) | Requires event-driven infrastructure |
| Native mobile apps | Responsive web covers mobile at launch |
| Community features, leaderboards | Retention features for post-traction |

Phase 2+ development is scoped and quoted separately after launch.

## Key Numbers

| Metric | Value |
| --- | --- |
| **Fixed development price** | AUD $15,000 |
| **Deposit to start** | AUD $3,000 |
| **Estimated timeline** | 6–7 weeks |
| **Monthly running costs (client's responsibility)** | AUD $300–$600/month |
| **Target market** | 38 US legal sports betting states |
| **Revenue model** | SaaS subscriptions via Stripe |

## How It Works — Concrete Example

> DraftKings is offering: **"Sign up, bet $5, get $200 in bonus bets"**
>
> The platform:
> 1. Knows this promo exists (scraped from DraftKings promo page)
> 2. Finds that the Lakers are +130 on DraftKings and the Celtics are -125 on FanDuel
> 3. Calculates: place the $200 free bet on Lakers at +130, hedge with $148 on Celtics at -125 on FanDuel
> 4. Result: **no matter who wins, the user profits ~$112**
> 5. AI generates a step-by-step card: "Open DraftKings → NBA → Lakers vs Celtics → place $200 free bet on Lakers Moneyline at +130..."

The user does the actual betting. We tell them exactly what to do.
