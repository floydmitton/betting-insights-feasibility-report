# Calculation Engine

The calculation engine is the core IP of the platform. It performs **deterministic mathematical calculations** — no AI, no prediction, no guesswork. Pure math applied to real-time odds data.

---

## Odds Conversion (Foundation)

All calculations require decimal odds internally. API data may arrive as American or decimal.

```
American to Decimal:
  If positive: decimal = (american / 100) + 1
    +130 → (130/100) + 1 = 2.30

  If negative: decimal = (100 / |american|) + 1
    -125 → (100/125) + 1 = 1.80

Decimal to American:
  If decimal >= 2.0: american = (decimal - 1) × 100
    2.30 → +130

  If decimal < 2.0: american = -100 / (decimal - 1)
    1.80 → -125

Implied Probability:
  If positive: prob = 100 / (american + 100)
    +130 → 100/230 = 43.5%

  If negative: prob = |american| / (|american| + 100)
    -125 → 125/225 = 55.6%
```

---

## Free Bet Conversion

The most common opportunity type. User has a free bet on one sportsbook and needs to convert it to cash.

**Key principle:** Free bets typically don't return the stake — only the profit. This changes the math compared to a normal bet.

### Formula

```
Given:
  free_bet_value = $200
  back_odds = +130 (decimal: 2.30) on DraftKings (free bet side)
  hedge_odds = -125 (decimal: 1.80) on FanDuel (real money side)

Free bet profit if back wins:
  back_profit = free_bet_value × (decimal_back - 1)
  back_profit = $200 × (2.30 - 1) = $200 × 1.30 = $260

Hedge stake to guarantee equal profit:
  hedge_stake = back_profit / decimal_hedge
  hedge_stake = $260 / 1.80 = $144.44

Scenarios:
  If back wins:  +$260 (free bet profit) - $144.44 (hedge lost) = $115.56
  If hedge wins: $144.44 × 1.80 = $260 returned → $260 - $144.44 = $115.56

Guaranteed profit: $115.56
Conversion rate: $115.56 / $200 = 57.8%
```

### Scanning Algorithm

```python
def find_best_free_bet_conversion(free_bet_book, free_bet_value, all_odds):
    best = None

    for event in all_events:
        for market in ['h2h', 'spreads', 'totals']:
            # Get best odds on the free bet book for each outcome
            back_options = get_odds(event, market, free_bet_book)

            for back in back_options:
                # Find best hedge on ANY other book
                hedge = get_best_opposing_odds(event, market, back.outcome,
                                                exclude_book=free_bet_book)
                if not hedge:
                    continue

                profit = calculate_free_bet_profit(
                    free_bet_value, back.decimal_price, hedge.decimal_price
                )

                if not best or profit > best.profit:
                    best = Opportunity(
                        back=back, hedge=hedge, profit=profit,
                        conversion_rate=profit/free_bet_value
                    )

    return best
```

---

## Pure Arbitrage Detection

Finds odds discrepancies between sportsbooks where betting both sides guarantees profit regardless of outcome.

### Formula

```
Given:
  odds_a = +150 (decimal: 2.50) on Book A for Team 1
  odds_b = +110 (decimal: 2.10) on Book B for Team 2

Arbitrage percentage:
  arb_pct = (1/decimal_a) + (1/decimal_b)
  arb_pct = (1/2.50) + (1/2.10) = 0.400 + 0.476 = 0.876

If arb_pct < 1.0 → ARBITRAGE EXISTS
  Profit margin = (1 - 0.876) × 100 = 12.4%

Optimal stakes for $1,000 total:
  stake_a = $1,000 × (1/decimal_a) / arb_pct
  stake_a = $1,000 × 0.400 / 0.876 = $456.62

  stake_b = $1,000 × (1/decimal_b) / arb_pct
  stake_b = $1,000 × 0.476 / 0.876 = $543.38

Scenarios:
  If Team 1 wins: $456.62 × 2.50 = $1,141.55 → profit = $141.55
  If Team 2 wins: $543.38 × 2.10 = $1,141.10 → profit = $141.10

Guaranteed profit: ~$141 (12.4% ROI)
```

### Note on Real-World Arbs

True arbitrage opportunities are rare, typically small (1–3%), and close quickly (seconds to minutes). The platform will find some, but the primary value proposition is bonus conversion, not pure arb.

---

## Deposit Match Conversion

User gets a deposit match (e.g., "100% match up to $500"). The bonus typically has a playthrough/rollover requirement.

### Formula

```
Given:
  match_value = $500 (bonus funds)
  playthrough = 1x (must wager $500 in bonus bets)

This is effectively the same as a free bet conversion,
but the playthrough requirement means you may need
multiple betting rounds to clear the bonus.

Simple 1x playthrough:
  → Same as free bet conversion: find best hedge, convert in one round
  → Expected conversion: 50-70% of match value

Higher playthrough (e.g., 10x):
  → Must wager $5,000 total
  → Each round loses a small amount to the vig
  → Expected loss per round ≈ 2-5% of stake
  → Net value = match_value - (playthrough × average_vig_loss)
```

---

## Odds Boost Exploitation

Sportsbook artificially inflates one side (e.g., "Lakers boosted from +130 to +200").

### Formula

```
Given:
  boosted_odds = +200 (decimal: 3.00) on DraftKings
  fair_market_odds for opposing side = -130 (decimal: 1.769) on FanDuel

Hedge calculation (same as standard arb):
  arb_pct = (1/3.00) + (1/1.769) = 0.333 + 0.565 = 0.898

  Since 0.898 < 1.0 → the boost creates an arb opportunity
  Profit margin = (1 - 0.898) × 100 = 10.2%
```

Odds boosts are the **easiest opportunities** for users — no bonus mechanics, just bet both sides.

---

## Second Chance Bet Conversion

"If your first bet loses, get a $1,000 bonus bet."

### Strategy

```
1. Place initial qualifying bet on a heavy underdog (high positive odds)
   → e.g., $50 on an underdog at +500

2. Hedge at another book on the favorite
   → e.g., $210 on the favorite at -120

3. If the underdog WINS:
   → $50 × 6.00 = $300 returned → big profit

4. If the underdog LOSES:
   → You lose the $50 initial bet
   → You win the $210 hedge (small profit/loss)
   → You receive the $1,000 bonus bet
   → Convert the $1,000 bonus bet using the free bet method
   → Expected conversion: $500-700 cash

Total expected value across both scenarios:
   → Weighted by probability of each outcome
```

---

## Edge Cases and Gotchas

| Issue | Impact | Handling |
| --- | --- | --- |
| **Minimum odds requirements** | Many promos require bets at -200 or longer | Filter out opportunities below min odds |
| **Maximum bet limits** | Sportsbooks cap wagers, especially on niche markets | Cannot validate via API; note in instructions |
| **Odds movement** | Odds change fast; stale data = wrong calculations | Timestamp all opps; warn if odds are >2 min old |
| **Multi-leg requirements** | Some promos require parlays (3+ legs) | Significantly more complex math; may be post-MVP |
| **Same Game Parlay promos** | Cannot be hedged across books | Flag as "manual only" opportunities |
| **State restrictions** | Promo available in NY but user is in NJ | Filter by user's state from profile |
