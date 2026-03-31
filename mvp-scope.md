# MVP Scope — What We Build

Everything below is included in the AUD $15,000 fixed development price. This is the complete, exhaustive list of what Whitecrow delivers. If it's not listed here, it's not in the MVP.

---

## 1. Odds Data Pipeline

**What we build:** An automated system that pulls real-time sports betting odds from TheOddsAPI every ~1 minute and stores them in the platform database.

| Detail | Specification |
| --- | --- |
| **Data source** | TheOddsAPI (single API) |
| **Sportsbooks covered** | All US books available via TheOddsAPI (~40+), including DraftKings, FanDuel, BetMGM, Caesars, ESPN BET, PointsBet, BetRivers, etc. |
| **Sports covered** | All major US sports available via the API: NFL, NBA, MLB, NHL, NCAA Football, NCAA Basketball, MMA, Soccer, Tennis |
| **Markets** | Moneyline (h2h), Spreads, Totals (over/under) |
| **Refresh rate** | ~1 minute via Vercel Cron |
| **Data stored** | Event name, teams, sport, commence time, sportsbook, market, outcome, American odds, decimal odds, timestamp |

**What we don't build (Phase 2+):**
- Sportradar integration
- OddsJam integration
- Event matching across multiple API providers
- Live/in-play odds (WebSocket streaming)
- Sub-1-minute refresh rates

---

## 2. Sportsbook Promo Scraping

**What we build:** Automated web scraping of promotional/bonus pages for 4 major US sportsbooks, plus an admin interface for manual entry and override.

| Detail | Specification |
| --- | --- |
| **Sportsbooks scraped** | DraftKings, FanDuel, BetMGM, Caesars |
| **Scraping tool** | Firecrawl |
| **Data extracted** | Promo title, bonus type, dollar value, wagering requirements, minimum odds, expiry date, eligible states, terms URL |
| **Refresh frequency** | Every 6 hours (promos don't change as fast as odds) |
| **Admin override** | Manual add/edit/disable for any promotion via admin panel |
| **Promo types tracked** | Sign-up free bets, first bet offers, welcome bonuses |

**What we don't build (Phase 2+):**
- Scraping beyond the 4 specified sportsbooks
- Deposit match offers (different conversion math)
- Odds boost offers
- Reload / loyalty offers
- Second chance bet offers
- Same Game Parlay promos
- Automated state-specific geolocation for scraping

**Important note on scraping:** Web scraping is inherently fragile. Sportsbook page structures change without warning. We build the initial scrapers and test them at launch. Ongoing scraper maintenance (when page layouts change) is either:
- Covered by a separate maintenance retainer (quoted separately), OR
- Handled by the client using the admin manual entry as a fallback

---

## 3. Signup Bonus Conversion Engine

**What we build:** A calculation engine that finds the mathematically optimal hedge bet to convert each sportsbook sign-up free bet into guaranteed cash.

| Detail | Specification |
| --- | --- |
| **Calculation type** | Free bet conversion (sign-up bonuses only) |
| **How it works** | For each active free bet promo, scans all current odds across all sportsbooks to find the hedge that maximises guaranteed profit |
| **Output per opportunity** | Back book, hedge book, sport, event, market, outcome A, outcome B, odds A, odds B, recommended stake A, recommended hedge stake B, guaranteed profit, conversion rate % |
| **Scanning scope** | Every event × every market × every sportsbook pair |
| **Edge cases handled** | Minimum odds requirements on promos, free bets that don't return stake, odds format conversion (American ↔ decimal) |
| **Stale data handling** | Opportunities auto-expire when underlying odds are >5 minutes old |

**What we don't build (Phase 2+):**
- Pure arbitrage detection (no-promo arb scanning)
- Deposit match conversion calculations
- Odds boost exploitation calculations
- Second chance bet conversion calculations
- Multi-leg / parlay promo calculations
- User-specific bankroll-adjusted stake recommendations
- Risk-adjusted expected value modelling

---

## 4. AI Instruction Cards

**What we build:** An AI layer that takes each calculated opportunity and generates a plain-English, step-by-step instruction card telling the user exactly what to do.

| Detail | Specification |
| --- | --- |
| **AI model** | Claude Sonnet or GPT-4o mini (finalised during build based on quality/cost testing) |
| **Input** | Structured JSON from the calculation engine (books, odds, stakes, profit — all pre-calculated) |
| **Output** | Numbered step-by-step instructions: which app to open, which sport/event/market, what to bet, how much |
| **Validation** | Every AI-generated card is validated against source data — all numbers must match exactly. Failed validation triggers regeneration or falls back to a deterministic template. |
| **Caching** | AI responses cached in Redis by input data hash. Same opportunity = same card for all users. Cache invalidated when odds change. |
| **Tone** | 6th-grade reading level, bolded key info, timestamp warning about odds movement |

**What we don't build (Phase 2+):**
- Personalised instruction tone/detail based on user experience level
- Multi-step guided flow (tick off each step as you go)
- Push notification delivery of cards
- AI-generated promo summaries for the Offer Library (MVP uses scraped/manual descriptions)

---

## 5. Public Marketing Website

**What we build:** A responsive, SEO-optimised marketing site that explains the product and drives signups.

| Page | What It Contains |
| --- | --- |
| **Home** | Hero section, 3-step "how it works" summary, feature highlights, stats bar (books covered, example savings), social proof section (placeholder slots), primary + secondary CTAs |
| **How It Works** | Full matched betting explainer with worked dollar example, FAQ accordion (8+ questions), responsible gambling disclaimer with 1-800-GAMBLER |
| **Pricing** | 3-column tier comparison (Free/Basic/Premium), monthly/annual toggle, each tier links directly to Stripe Checkout |
| **FAQ** | 15+ questions covering: how it works, sportsbook coverage, getting started, billing, responsible gambling |
| **Blog** | Archive page with category filters, individual post template with formatting — **templates only, no content written** |
| **Contact** | Contact form (sends email to client's nominated address), company information |

**What we deliver:** Fully built, responsive pages (mobile, tablet, desktop). Ready for content.

**What the client provides:** All copy, logo, brand colours, fonts, and any photography/imagery. Blog content. Testimonials or social proof.

---

## 6. User Portal (Authenticated)

**What we build:** The core product — a login-gated dashboard where subscribers view and act on opportunities.

| Feature | What It Does |
| --- | --- |
| **Sign up / Login** | Email + password and Google OAuth via Supabase Auth. Email verification. Password reset. |
| **Dashboard** | Feed of current signup bonus conversion opportunities, sorted by profit (highest first). Each opportunity shown as an AI-generated card with books, odds, stakes, and profit. Free tier: 3 opportunities visible, rest blurred with upgrade CTA. |
| **Offer Library** | List of currently active sportsbook sign-up bonuses across the 4 scraped books. Shows: book, offer title, value, wagering requirement, expiry, and estimated conversion value. |
| **Free Bet Calculator** | Manual calculator: user inputs free bet value + back odds + hedge odds → outputs recommended stakes and guaranteed profit. Available to all tiers. |
| **My Account** | Email (read-only), display name, notification preferences (daily digest on/off). |
| **Billing** | Embedded Stripe Customer Portal: view plan, update payment method, view invoices, upgrade/downgrade/cancel. |

**What we don't build (Phase 2+):**
- Onboarding wizard (select state, select books, set bankroll)
- Personalised feed filtering (by user's state, books, bankroll)
- Advanced calculators (arb calculator, EV calculator, deposit match calculator)
- Opportunity Finder with search/filter (sport, book, min profit, bonus type)
- Profit tracking / betting history
- Push notifications

---

## 7. Admin Panel

**What we build:** An internal-only area for platform management.

| Feature | What It Does |
| --- | --- |
| **User list** | View all registered users. Search by email. See tier, signup date, subscription status. |
| **Offer management** | Add, edit, and disable sportsbook promotions manually. This is the critical fallback when scrapers break. |
| **Platform health** | Last successful API fetch time, last successful scrape per book, total active opportunities, error count. |
| **Basic metrics** | Total users, users per tier, active subscriptions count. |

**What we don't build (Phase 2+):**
- MRR / revenue dashboards
- User analytics (engagement, retention, churn)
- Automated alerts when scrapers fail
- Bulk user management tools

---

## 8. Subscription & Billing System

**What we build:** Full Stripe Billing integration with 3 tiers, webhook-driven access control, and database-level enforcement.

| Tier | Access | Suggested Price (client sets final) |
| --- | --- | --- |
| **Free** | 3 opportunities/day, basic calculator, offer library (view only) | $0 |
| **Basic** | All opportunities, full offer library with conversion values, daily email digest | $29–49/month |
| **Premium** | Everything in Basic (reserved for Phase 2 premium features) | $79–149/month |

| Billing Feature | Included |
| --- | --- |
| Stripe Checkout (hosted payment page) | Yes |
| Monthly + annual pricing toggle | Yes |
| Stripe Customer Portal (manage subscription) | Yes |
| Webhook handling (subscribe, upgrade, downgrade, cancel, payment failure) | Yes |
| Tier enforcement at database level (Row Level Security) | Yes |
| Stripe signature verification on all webhooks | Yes |

---

## 9. Email System

**What we build:**

| Email | Trigger | Tiers |
| --- | --- | --- |
| Welcome email | User signs up | All |
| Email verification | User registers with email/password | All |
| Password reset | User requests reset | All |
| Subscription confirmed | Successful Stripe payment | Paid |
| Payment failed | Stripe payment failure | Paid |
| Subscription cancelled | User cancels | Paid |
| Daily digest | Cron (morning) — top 5 opportunities | Basic + Premium |

**What we don't build (Phase 2+):**
- Real-time high-value opportunity alerts
- Weekly summary reports
- Re-engagement / win-back emails
- Referral program emails

---

## 10. Production Deployment & Handover

**What we deliver at launch:**

| Deliverable | Detail |
| --- | --- |
| **Live production site** | Deployed on Vercel, connected to client's custom domain |
| **SSL / HTTPS** | Automatic via Vercel |
| **Database** | Supabase project with all migrations applied |
| **Monitoring** | Sentry error tracking + Vercel Analytics configured |
| **Credential handover** | Full access to: Vercel account, Supabase project, Stripe dashboard, Resend account, TheOddsAPI account, Firecrawl account, GitHub repo, LLM API account |
| **Source code** | Full ownership transferred. Client owns all code and IP. |
| **30-day bug warranty** | Post-launch bugs in delivered features are fixed at no additional cost for 30 days |
