# AI Layer

The AI layer sits on top of the calculation engine. It takes structured data (numbers, books, odds, stakes) and generates human-readable instruction cards. It never performs calculations.

---

## Architecture

```
Calculation Engine
  ↓ structured JSON (book, odds, stakes, profit)
  ↓
Hash the input → check Redis cache
  ↓ cache miss?
  ↓
LLM API (Claude Sonnet or GPT-4o mini)
  ↓ AI-generated text
  ↓
Validate output (numbers match input)
  ↓ if valid
  ↓
Cache in Redis → Store in opportunities.ai_card
```

## Input → Output Example

### Input (from calculation engine)

```json
{
  "type": "free_bet_conversion",
  "promo": "DraftKings $200 Free Bet",
  "book_a": "DraftKings",
  "book_b": "FanDuel",
  "sport": "NBA",
  "event": "Lakers vs Celtics",
  "market": "Moneyline",
  "outcome_a": "Lakers",
  "odds_a": "+130",
  "outcome_b": "Celtics",
  "odds_b": "-125",
  "stake_a": "$200.00 (free bet)",
  "stake_b": "$148.15 (real money)",
  "guaranteed_profit": "$112.59",
  "conversion_rate": "56.3%"
}
```

### Output (AI-generated card)

> **Convert your DraftKings $200 Free Bet → $112.59 cash**
>
> 1. Open **DraftKings** → NBA → Lakers vs Celtics
> 2. Place your **$200 free bet** on **Lakers Moneyline at +130**
> 3. Open **FanDuel** → NBA → Lakers vs Celtics
> 4. Place **$148.15 real money** on **Celtics Moneyline at -125**
> 5. No matter who wins, you profit **$112.59**
>
> *Odds valid as of 2:35 PM ET. Place both bets quickly — odds move fast.*

## Prompt Engineering

### System Prompt (template)

```
You are a sports betting instruction writer. You generate clear,
step-by-step instructions for matched betting opportunities.

Rules:
- Use ONLY the data provided. Never invent odds, stakes, or amounts.
- Every number in your output must exactly match the input data.
- Write at a 6th-grade reading level.
- Use bold for sportsbook names, stake amounts, and profit.
- Always include a timestamp warning about odds movement.
- Never give gambling advice or predict outcomes.
- Never suggest the user bet more than the recommended stake.
```

### Per-Type Templates

Different opportunity types need different prompt structures:

- **Free bet conversion** → "Convert your [book] free bet" framing
- **Pure arb** → "Guaranteed profit" framing, emphasize speed
- **Odds boost** → "Take advantage of the boost" framing
- **Deposit match** → Multi-step flow with playthrough explanation
- **Second chance** → Two-scenario explanation with expected values

## Validation Layer

**Critical:** The AI output must be validated before serving to users.

```python
def validate_ai_card(input_data, ai_output):
    """Ensure AI hasn't hallucinated any numbers."""
    errors = []

    # Check all monetary amounts appear in output
    for field in ['stake_a', 'stake_b', 'guaranteed_profit']:
        amount = input_data[field]
        if amount not in ai_output:
            errors.append(f"Missing {field}: {amount}")

    # Check odds appear correctly
    for field in ['odds_a', 'odds_b']:
        odds = input_data[field]
        if odds not in ai_output:
            errors.append(f"Missing {field}: {odds}")

    # Check book names
    for field in ['book_a', 'book_b']:
        book = input_data[field]
        if book not in ai_output:
            errors.append(f"Missing {field}: {book}")

    if errors:
        # Log and regenerate, or fall back to template
        return False, errors

    return True, []
```

If validation fails, fall back to a **deterministic template** (no AI) rather than serving incorrect information.

## Caching Strategy

```
Cache key = SHA256(JSON.stringify(sorted_input_data))

Cache TTL:
  - Odds-dependent cards: 3 minutes (odds move fast)
  - Promo narrations: 6 hours (promos change slowly)
  - Calculator explanations: 24 hours (static formula explanations)

Cache scope: SHARED across all users
  - Same opportunity = same card for everyone
  - Personalisation happens at the feed level (filtering), not the card level
```

### Cost Impact

Without caching, every dashboard load for every user calls the LLM. With caching:

| Scenario | Without Cache | With Cache |
| --- | --- | --- |
| 100 users, 50 opps | 5,000 LLM calls/day | ~200 LLM calls/day |
| 1,000 users, 50 opps | 50,000 LLM calls/day | ~200 LLM calls/day |
| 10,000 users, 50 opps | 500,000 LLM calls/day | ~250 LLM calls/day |

The number of LLM calls scales with **opportunities**, not **users**.

## Model Selection

| Model | Strengths | Weaknesses | Cost (est.) |
| --- | --- | --- | --- |
| **Claude Sonnet 4** | Excellent instruction following, consistent formatting, handles structured data well | Slightly higher latency | ~$3 per 1M input tokens |
| **GPT-4o mini** | Very fast, very cheap, good for templated tasks | Less consistent on edge cases, occasional format drift | ~$0.15 per 1M input tokens |

**Recommendation:** Start with GPT-4o mini for cost efficiency. Switch to Claude Sonnet if quality issues arise. The caching layer means the cost difference is minimal at scale anyway.
