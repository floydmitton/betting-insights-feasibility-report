# Phase 2+ — Future Development

Everything below is **not included** in the AUD $15,000 MVP build. These are natural extensions scoped and quoted separately after the MVP is live and generating revenue.

---

## Phase 2 — Enhanced Conversion Engine

**When:** Post-launch, once MVP is validated with paying subscribers\
**Estimated effort:** 3–5 weeks\
**Quoted separately**

| Feature | What It Adds |
| --- | --- |
| **Deposit match conversion** | New calculation type: finds optimal strategy to convert deposit match bonuses (e.g., "100% match up to $500") into cash, accounting for playthrough/wagering requirements |
| **Odds boost exploitation** | New calculation type: detects when a sportsbook boosts odds above fair value, finds the optimal hedge to lock in guaranteed profit from the boost |
| **Second chance bet conversion** | New calculation type: calculates the optimal strategy for "if your bet loses, get $X in bonus bets" promos — place qualifying bet on underdog, hedge, and convert the bonus if it triggers |
| **Pure arbitrage detection** | Scans all sportsbook pairs for odds discrepancies that guarantee profit without any promo required. These are rare and close fast — requires fastest possible data refresh |
| **Additional sportsbook scraping** | Extend Offer Library scraping beyond the initial 4 books — ESPN BET, PointsBet, BetRivers, Bet365, Hard Rock, etc. Each book requires custom scraping logic |

---

## Phase 3 — Personalisation & Multi-API

**When:** Once Phase 2 calculation types are live\
**Estimated effort:** 3–4 weeks\
**Quoted separately**

| Feature | What It Adds |
| --- | --- |
| **User onboarding wizard** | 3-step flow on first login: select US state → select which sportsbook accounts you have → set bankroll. Saves to profile. |
| **Personalised opportunity feed** | Dashboard filters opportunities to only show ones relevant to the user's state, their active sportsbooks, and scaled to their bankroll |
| **Sportradar API integration** | Second odds data source — richer event metadata, live scores, broader market coverage. Requires event matching/normalisation across two providers |
| **OddsJam API integration** | Third odds data source — strong line shopping data. Further event matching complexity |
| **Multi-API event matching** | The system that maps "Lakers vs Celtics" (TheOddsAPI) to "Los Angeles Lakers at Boston Celtics" (Sportradar) to "LAL @ BOS" (OddsJam) as the same event |
| **Bankroll-adjusted recommendations** | Stake recommendations scaled to each user's available bankroll rather than the promo's face value |

---

## Phase 4 — Engagement & Retention

**When:** Once the product has 100+ subscribers\
**Estimated effort:** 3–5 weeks\
**Quoted separately**

| Feature | What It Adds |
| --- | --- |
| **Real-time high-value alerts** | Email and/or push notifications when a new opportunity above the user's profit threshold appears — "act fast" urgency messaging |
| **Advanced Opportunity Finder** | Full search and filter interface: filter by sport, sportsbook, minimum profit, bonus type, market. Expandable rows with full AI instructions |
| **Advanced calculators** | Arbitrage calculator, EV calculator, deposit match calculator, odds boost calculator — all with explanatory output |
| **Profit tracking** | Users log completed bets and track cumulative profit over time. Dashboard shows ROI and total extracted value |
| **Community features** | Discord bot integration, leaderboards (opt-in), user success stories |

---

## Phase 5 — Scale & Mobile

**When:** Once revenue justifies the investment\
**Estimated effort:** 8–12 weeks per platform\
**Quoted separately**

| Feature | What It Adds |
| --- | --- |
| **Native iOS app** | Full-featured mobile app with push notifications, optimised for quick opportunity viewing and action |
| **Native Android app** | Same as iOS |
| **Live in-play odds** | WebSocket-based real-time odds streaming for live/in-play events — fundamentally different infrastructure from REST polling |
| **Sub-minute odds refresh** | Upgrade from 1-minute polling to 5–15 second refresh via premium API tiers |
| **Automated bet placement** | Direct integration with sportsbook APIs (where available) to place bets on the user's behalf. Massive legal/compliance implications — requires separate legal review |

---

## How Phase 2+ Pricing Works

Each phase is scoped based on the client's priorities at the time. The process:

1. Client identifies which features they want next
2. Whitecrow provides a detailed scope and fixed-price quote
3. Client approves and development begins
4. Same milestone-based process as the MVP (design sign-off → staging review → launch)

Alternatively, a **monthly retainer** can be arranged for ongoing development, maintenance, and scraper upkeep — giving the client a predictable budget and continuous improvement.

---

## Recommended Priority Order

Based on revenue impact and technical dependencies:

| Priority | Feature | Why First |
| --- | --- | --- |
| **1** | Deposit match + odds boost calculations | Doubles the number of opportunities users see — directly increases perceived value |
| **2** | Additional sportsbook scraping (2–3 more books) | More promos = more opportunities = more subscriber value |
| **3** | Personalised feed (state + books filter) | Reduces noise — users only see what's relevant to them |
| **4** | Real-time alerts | Drives engagement and urgency — "I need to keep my subscription to not miss opportunities" |
| **5** | Profit tracking | Retention — users see their cumulative value and don't churn |
