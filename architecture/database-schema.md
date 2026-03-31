# Database Schema

All data stored in **Supabase (managed PostgreSQL)** with Row Level Security policies enforcing access control at the database layer.

---

## Users Table

Stores core user identity and preferences.

```sql
CREATE TABLE users (
  id              UUID PRIMARY KEY,        -- from auth provider
  email           TEXT NOT NULL UNIQUE,
  display_name    TEXT,
  us_state        TEXT,                     -- e.g., "NY", "NJ", "PA"
  bankroll        DECIMAL(10,2),            -- starting bankroll in USD
  tier            TEXT DEFAULT 'free',      -- free | basic | premium
  stripe_customer_id TEXT UNIQUE,
  created_at      TIMESTAMPTZ DEFAULT NOW(),
  updated_at      TIMESTAMPTZ DEFAULT NOW()
);
```

## User Sportsbooks Table

Which sportsbook accounts each user has — used to filter their opportunity feed.

```sql
CREATE TABLE user_sportsbooks (
  user_id         UUID REFERENCES users(id) ON DELETE CASCADE,
  sportsbook_id   UUID REFERENCES sportsbooks(id) ON DELETE CASCADE,
  created_at      TIMESTAMPTZ DEFAULT NOW(),
  PRIMARY KEY (user_id, sportsbook_id)
);
```

## Sportsbooks Table

Master list of all covered sportsbooks.

```sql
CREATE TABLE sportsbooks (
  id              UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  name            TEXT NOT NULL,             -- e.g., "DraftKings"
  slug            TEXT NOT NULL UNIQUE,      -- e.g., "draftkings"
  logo_url        TEXT,
  website_url     TEXT,
  states          TEXT[],                    -- states where this book operates
  created_at      TIMESTAMPTZ DEFAULT NOW()
);
```

## Odds Table

Raw normalized odds data from all API sources. Refreshed every ~1 minute.

```sql
CREATE TABLE odds (
  id              UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  event_id        TEXT NOT NULL,             -- normalized event identifier
  sport           TEXT NOT NULL,             -- e.g., "basketball_nba"
  event_name      TEXT NOT NULL,             -- e.g., "Lakers vs Celtics"
  commence_time   TIMESTAMPTZ,
  sportsbook_id   UUID REFERENCES sportsbooks(id),
  market          TEXT NOT NULL,             -- e.g., "h2h", "spreads", "totals"
  outcome         TEXT NOT NULL,             -- e.g., "Lakers", "Over 210.5"
  price           INTEGER NOT NULL,          -- American odds (+130, -125)
  decimal_price   DECIMAL(8,4) NOT NULL,     -- converted decimal odds
  source_api      TEXT NOT NULL,             -- "theoddsapi" | "sportradar" | "oddsjam"
  fetched_at      TIMESTAMPTZ DEFAULT NOW()
);

-- Index for fast opportunity scanning
CREATE INDEX idx_odds_event_market ON odds(event_id, market);
CREATE INDEX idx_odds_sport ON odds(sport);
CREATE INDEX idx_odds_sportsbook ON odds(sportsbook_id);
```

## Promotions Table

Active sportsbook promotions — the Offer Library data source.

```sql
CREATE TABLE promotions (
  id              UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  sportsbook_id   UUID REFERENCES sportsbooks(id),
  title           TEXT NOT NULL,             -- "Bet $5, Get $200 in Bonus Bets"
  promo_type      TEXT NOT NULL,             -- free_bet | deposit_match | odds_boost | second_chance | reload
  value_usd       DECIMAL(10,2),            -- face value of the bonus
  wagering_req    TEXT,                      -- "1x playthrough", "none"
  min_odds        INTEGER,                   -- minimum odds required (American)
  min_deposit     DECIMAL(10,2),
  expiry_date     DATE,
  states          TEXT[],                    -- which states this promo is available in
  terms_url       TEXT,
  difficulty      TEXT DEFAULT 'medium',     -- easy | medium | hard
  conversion_rate DECIMAL(5,4),              -- estimated % convertible to cash
  estimated_value DECIMAL(10,2),             -- value_usd × conversion_rate
  source          TEXT DEFAULT 'manual',     -- scraped | manual
  ai_summary      TEXT,                      -- AI-generated explanation
  active          BOOLEAN DEFAULT TRUE,
  created_at      TIMESTAMPTZ DEFAULT NOW(),
  updated_at      TIMESTAMPTZ DEFAULT NOW()
);
```

## Opportunities Table

Calculated matched betting and arbitrage opportunities — the core product data.

```sql
CREATE TABLE opportunities (
  id              UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  promotion_id    UUID REFERENCES promotions(id),  -- null for pure arbs
  type            TEXT NOT NULL,             -- free_bet_conversion | arb | deposit_match | odds_boost | second_chance
  sport           TEXT NOT NULL,
  event_name      TEXT NOT NULL,
  commence_time   TIMESTAMPTZ,
  book_a          UUID REFERENCES sportsbooks(id),  -- bonus/back bet book
  book_b          UUID REFERENCES sportsbooks(id),  -- hedge book
  market          TEXT NOT NULL,
  outcome_a       TEXT NOT NULL,
  outcome_b       TEXT NOT NULL,
  odds_a          INTEGER NOT NULL,          -- American odds
  odds_b          INTEGER NOT NULL,
  stake_a         DECIMAL(10,2) NOT NULL,    -- recommended back stake
  stake_b         DECIMAL(10,2) NOT NULL,    -- recommended hedge stake
  expected_profit DECIMAL(10,2) NOT NULL,
  roi_pct         DECIMAL(6,2),
  states          TEXT[],
  status          TEXT DEFAULT 'active',     -- active | expired | odds_moved
  ai_card         TEXT,                      -- cached AI instruction card
  ai_card_hash    TEXT,                      -- hash of input data for cache invalidation
  created_at      TIMESTAMPTZ DEFAULT NOW(),
  expires_at      TIMESTAMPTZ
);

-- Indexes for dashboard queries
CREATE INDEX idx_opps_status ON opportunities(status);
CREATE INDEX idx_opps_type ON opportunities(type);
CREATE INDEX idx_opps_profit ON opportunities(expected_profit DESC);
CREATE INDEX idx_opps_states ON opportunities USING GIN(states);
```

## Subscriptions Table

Synced from Stripe via webhooks.

```sql
CREATE TABLE subscriptions (
  id                      UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id                 UUID REFERENCES users(id) ON DELETE CASCADE,
  stripe_subscription_id  TEXT UNIQUE,
  plan                    TEXT NOT NULL,      -- free | basic | premium
  status                  TEXT NOT NULL,      -- active | past_due | canceled | trialing
  current_period_end      TIMESTAMPTZ,
  created_at              TIMESTAMPTZ DEFAULT NOW()
);
```

## Notification Preferences Table

```sql
CREATE TABLE notification_preferences (
  user_id             UUID PRIMARY KEY REFERENCES users(id) ON DELETE CASCADE,
  daily_digest        BOOLEAN DEFAULT TRUE,
  high_value_alerts   BOOLEAN DEFAULT TRUE,
  min_alert_profit    DECIMAL(10,2) DEFAULT 20.00
);
```

---

## Row Level Security Policies

Example RLS policies for tier-gated access:

```sql
-- Users can only read their own profile
CREATE POLICY "users_read_own" ON users
  FOR SELECT USING (auth.uid() = id);

-- Opportunities: free tier sees max 5 per day
CREATE POLICY "opps_free_tier" ON opportunities
  FOR SELECT USING (
    EXISTS (
      SELECT 1 FROM users
      WHERE users.id = auth.uid()
      AND users.tier IN ('basic', 'premium')
    )
    OR (
      -- Free tier: limited access
      EXISTS (
        SELECT 1 FROM users
        WHERE users.id = auth.uid()
        AND users.tier = 'free'
      )
      AND id IN (
        SELECT id FROM opportunities
        WHERE status = 'active'
        ORDER BY expected_profit DESC
        LIMIT 5
      )
    )
  );

-- Promotions: full conversion data only for paid tiers
CREATE POLICY "promos_paid_details" ON promotions
  FOR SELECT USING (TRUE);
  -- Note: conversion_rate and estimated_value columns
  -- filtered at API level for free tier (return null)
```
