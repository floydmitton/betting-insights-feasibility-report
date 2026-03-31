# AI Capabilities

The platform includes an AI engine that analyses real-time odds data and generates opportunity cards in plain English. The AI **never performs calculations** — the deterministic calculation engine handles all math. The AI is a **narration and explanation layer** on top of structured data.

---

## Capability Breakdown

### Opportunity Analysis

For each matched betting or arbitrage opportunity identified by the calculation engine, the AI generates a plain-English card explaining:
- What the opportunity is
- Why it has positive expected value
- What the subscriber should do
- The specific sportsbooks involved
- Recommended stake on each side
- Expected profit
- Risk summary

### Step-by-Step Execution Instructions

The AI generates a numbered walkthrough for each opportunity:

1. Which sportsbook to open first
2. Which market to navigate to
3. What odds to accept
4. What amount to enter
5. What to do at the second book

The level of detail and assumed user knowledge is configurable.

### Personalised Feed

The AI filters and ranks opportunities based on each subscriber's profile:
- Their US state
- Active sportsbook accounts
- Nominated bankroll

Opportunities requiring a book the subscriber doesn't have are suppressed or flagged as "new account opportunities."

### Offer Library Narration

Each promotional offer can include an AI-generated summary:
- Plain-English explanation of the offer
- Recommended conversion strategy
- Estimated cash value after conversion
- Difficulty level assessment

### Follow-Through Options

The extent to which the AI guides users through completion ranges from:
- **Simple:** One-time instruction card
- **Guided:** Multi-step flow where the user ticks off each step as they complete it

### Model Selection

The specific LLM will be finalised based on pre-build testing:

| Model | Pros | Cons |
| --- | --- | --- |
| **Claude Sonnet (Anthropic)** | Excellent instruction-following, consistent tone, good at structured output | Slightly higher latency |
| **GPT-4o mini (OpenAI)** | Fast, cheap, good enough for templated narration | Less consistent on edge cases |

Decision based on accuracy, response consistency, and cost per query at expected usage volume.

### Caching & Cost Control

AI responses for identical underlying data are **cached in Redis and shared across users**. This is the key cost control mechanism:

- Same DraftKings promo + same odds = same AI card for all users
- Cache key = hash of the structured input data
- When odds change, hash changes, card is regenerated
- TTL: 2–5 minutes for odds-dependent cards, longer for promo narrations

**Cost projection at scale:**

| Subscribers | Without Cache | With Cache |
| --- | --- | --- |
| 100 | ~$50/month | ~$50/month |
| 1,000 | ~$500/month | ~$80/month |
| 10,000 | ~$5,000/month | ~$120/month |

Caching makes AI cost nearly flat as subscribers grow.

---

## How the AI Layer Actually Works

The calculation engine produces a structured JSON object:

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
  "if_a_wins": "$112.59 profit",
  "if_b_wins": "$112.59 profit",
  "guaranteed_profit": "$112.59",
  "conversion_rate": "56.3%"
}
```

The AI takes that object and produces:

> **Convert your DraftKings $200 Free Bet → $112.59 cash**
>
> 1. Open DraftKings → NBA → Lakers vs Celtics
> 2. Place your $200 free bet on Lakers Moneyline at +130
> 3. Open FanDuel → NBA → Lakers vs Celtics
> 4. Place $148.15 real money on Celtics Moneyline at -125
> 5. No matter who wins, you profit $112.59
>
> *Odds valid as of 2:35 PM ET. Place both bets quickly — odds move fast.*

**Critical validation requirement:** The AI output must be validated against the source data to ensure it never contradicts the calculation engine. If the calc says $148.15, the AI must say $148.15 — never a rounded or hallucinated number.

---

## Developer Feasibility Notes

**Effort: MEDIUM** — The architecture (calc engine → structured JSON → LLM narration → cache) is sound and well-established. The hard part is prompt engineering for consistency across thousands of generated cards, and building the validation layer to catch hallucinations.

**Estimated time:** 4–6 days for integration, prompt engineering, caching, and validation.
