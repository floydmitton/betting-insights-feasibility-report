# What's Moderately Hard

These components are achievable but require careful implementation and testing.

---

## TheOddsAPI Integration + Data Pipeline

A single API integration is straightforward. The pipeline around it — cron scheduling, normalisation, storage, stale data handling — is where the work lives.

### What's Involved

1. Vercel Cron triggers every ~1 minute
2. Serverless function calls TheOddsAPI for each active sport
3. Response normalised: odds converted to both American + decimal, sportsbook names mapped to database IDs
4. Upserted to Supabase `odds` table (replace stale rows per event + book + market)
5. Stale odds (>5 min old) flagged and excluded from calculations
6. Errors logged to Sentry; last-fetch timestamp exposed in admin

### Constraints

- **Vercel function timeout:** 60 seconds on Pro plan. Fetching all sports in a single function invocation may push close to this limit.
- **Mitigation:** Split into one function per sport group, running in parallel. Each stays well within the timeout.
- **API rate limits:** TheOddsAPI limits vary by plan. Need to stay within monthly request budget while maintaining ~1 min freshness.

**Effort:** 3–4 days including error handling and monitoring.

---

## Signup Bonus Conversion Engine

The math itself is simple (free bet conversion formula). The hard part is domain edge cases.

### The Core Formula

```
free_bet_profit = free_bet_value × (decimal_back_odds - 1)
hedge_stake = free_bet_profit / decimal_hedge_odds
guaranteed_profit = free_bet_profit - hedge_stake
```

### Edge Cases That Require Careful Handling

| Edge Case | Complexity | How We Handle It |
| --- | --- | --- |
| **Free bets don't return stake** | Medium | Different formula from a normal bet — we use the "SNR" (stake not returned) calculation |
| **Minimum odds requirements** | Low | Filter out opportunities where hedge or back odds fall below the promo's minimum |
| **Odds format conversion** | Low | Normalise everything to decimal internally, display as American |
| **Two-way vs three-way markets** | Medium | Moneyline in soccer/hockey has a draw option — need to handle or exclude |
| **Same-book hedging** | Low | Most promos don't allow hedging on the same book — exclude same-book pairs |
| **Odds movement between calculation and user action** | N/A (at MVP) | We timestamp opportunities and warn users that odds may have moved. Real-time validation is Phase 2+ |

### Domain Knowledge Risk

If the developer isn't experienced with matched betting mechanics, the calculator may produce wrong numbers in edge cases. Wrong numbers = users lose money.

**Mitigation:** Build a comprehensive test suite with manually verified expected outputs for at least 20 scenarios. Include standard cases and all known edge cases.

**Effort:** 4–5 days including the test suite.

---

## AI Instruction Cards

The architecture is sound: calc engine → structured JSON → LLM → validation → cache. The work is in prompt engineering and the validation layer.

### What Makes It Moderate (Not Easy)

1. **Prompt engineering for consistency:** Different promo types and scenarios need consistent, accurate output across thousands of generated cards. Getting the prompts right takes iteration.

2. **Validation layer:** Every AI-generated card must be checked against the source data. All dollar amounts, odds, and book names must match exactly. This is a parsing + comparison step that catches hallucinations.

3. **Fallback strategy:** When AI fails validation (or the LLM API is down), serve a deterministic template card instead. This template is less readable but always correct.

4. **Cache key design:** The cache key must capture all inputs that affect the output — if any underlying data changes, the cache must invalidate. Hash the full input JSON.

### What Makes It NOT Hard

- The AI is doing narration, not calculation. All numbers are pre-computed.
- Input and output are structured and predictable — not open-ended generation.
- Caching means we only call the LLM for genuinely new opportunities.

**Effort:** 4–5 days including prompt iteration, validation layer, caching, and fallback.
