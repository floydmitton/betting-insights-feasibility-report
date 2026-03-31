# Architecture Diagram

## Full System Map

```
┌─────────────────────────────────────────────────────────────┐
│                      PUBLIC WEBSITE                          │
│  Next.js (SSR/SSG) on Vercel                                │
│  Home, How It Works, Features, Pricing, FAQ, Blog, Contact  │
└────────────────────────────┬────────────────────────────────┘
                             │ CTA → Sign Up
                             ▼
┌─────────────────────────────────────────────────────────────┐
│                       AUTH LAYER                             │
│  Azure Entra External ID (or Supabase Auth)                 │
│  Email/password + Google OAuth                              │
│  JWT issued on login → validated on every API request        │
└────────────────────────────┬────────────────────────────────┘
                             │ Authenticated
                             ▼
┌─────────────────────────────────────────────────────────────┐
│                       USER PORTAL                            │
│  Next.js authenticated pages                                │
│                                                              │
│  ┌────────────┐  ┌───────────────┐  ┌───────────────────┐  │
│  │  Dashboard  │  │  Opp Finder   │  │  Offer Library    │  │
│  │ (filtered   │  │ (search/      │  │ (active promos    │  │
│  │  opp feed)  │  │  filter all)  │  │  by state)        │  │
│  └─────┬──────┘  └───────┬───────┘  └─────────┬─────────┘  │
│        │                 │                     │             │
│  ┌─────┴──────┐  ┌──────┴──────┐  ┌──────────┴──────────┐  │
│  │ Calculators │  │  My Account │  │  Billing (Stripe)   │  │
│  └────────────┘  └─────────────┘  └─────────────────────┘  │
└────────────────────────────┬────────────────────────────────┘
                             │ API requests
                             ▼
┌─────────────────────────────────────────────────────────────┐
│                NEXT.JS API ROUTES / tRPC                     │
│  (Vercel Serverless Functions)                              │
│                                                              │
│  • Validates JWT on every request                            │
│  • Checks user tier via Supabase                             │
│  • Returns data gated by tier                                │
│  • Rate limited via Upstash Redis                            │
└────────┬─────────────────┬──────────────────┬───────────────┘
         │                 │                  │
         ▼                 ▼                  ▼
┌──────────────┐   ┌──────────┐   ┌──────────────┐
│   SUPABASE   │   │  REDIS   │   │   LLM API    │
│ (PostgreSQL) │   │ (Upstash)│   │(Claude/GPT)  │
│              │   │          │   │              │
│ • Users      │   │ • AI     │   │ • Generates  │
│ • Profiles   │   │   cache  │   │   opp cards  │
│ • Odds data  │   │ • Rate   │   │ • Narrates   │
│ • Promos     │   │   limits │   │   offers     │
│ • Opps       │   │          │   │              │
│ • Subs/tiers │   │          │   │              │
└──────┬───────┘   └──────────┘   └──────────────┘
       │
       │ Written to by
       ▼
┌─────────────────────────────────────────────────────────────┐
│                DATA PIPELINE (Vercel Cron)                    │
│  Runs every ~1 minute                                       │
│                                                              │
│  1. Poll TheOddsAPI → normalize → write to Supabase         │
│  2. Poll Sportradar → normalize → write to Supabase         │
│  3. Poll OddsJam → normalize → write to Supabase            │
│  4. Run calculation engine:                                  │
│     - Scan all odds pairs across books                       │
│     - Identify arb opportunities                             │
│     - Identify best hedges for each active bonus type        │
│     - Calculate stakes, expected profit, EV                  │
│     - Write opportunities to Supabase                        │
│  5. For new opportunities → call LLM API → cache result     │
└────────────────────────────┬────────────────────────────────┘
                             │ Pulls from
                             ▼
┌─────────────────────────────────────────────────────────────┐
│                      EXTERNAL APIS                           │
│                                                              │
│  TheOddsAPI ─── real-time odds, 40+ US sportsbooks          │
│  Sportradar ─── fallback odds + live scores/event data      │
│  OddsJam    ─── secondary odds feed, line shopping          │
│  Firecrawl  ─── scrapes sportsbook promo pages              │
│  Stripe     ─── billing, subscriptions, webhooks            │
│  Resend     ─── transactional + alert emails                │
└─────────────────────────────────────────────────────────────┘
```

## Request Flow: User Loads Dashboard

```
1. Browser sends GET /api/opportunities
2. Next.js API route validates JWT (Azure Entra / Supabase Auth)
3. Extracts user_id from token
4. Queries Supabase: SELECT * FROM opportunities
   → RLS policy auto-filters by user's tier
   → Application filter by user's state + active books
5. Returns personalised, tier-gated opportunity list
6. React renders opportunity cards with cached AI text
```

## Request Flow: Stripe Subscription Change

```
1. User clicks "Upgrade to Premium" → Stripe Checkout
2. User completes payment on Stripe-hosted page
3. Stripe fires webhook: checkout.session.completed
4. Next.js webhook handler:
   a. Verifies Stripe signature
   b. Looks up user by stripe_customer_id
   c. Updates subscriptions table (plan: "premium")
   d. Updates users.tier = "premium"
5. Next page load: RLS policies now grant premium-tier data
```
