# Data Pipeline

The data pipeline is the heartbeat of the platform. It runs every ~60 seconds via Vercel Cron, pulling odds from external APIs, normalizing the data, calculating opportunities, and generating AI cards.

---

## Pipeline Flow

```
Every 60 seconds (Vercel Cron triggers):

┌──────────────────────────────────────────────────┐
│ STEP 1: FETCH                                     │
│                                                   │
│  ┌─ GET TheOddsAPI /sports/{sport}/odds           │
│  ├─ GET Sportradar /odds/...                      │
│  └─ GET OddsJam /odds/...                         │
│                                                   │
│  Run in parallel. Each has independent error       │
│  handling — one API failing doesn't block others.  │
└──────────────────────┬───────────────────────────┘
                       ▼
┌──────────────────────────────────────────────────┐
│ STEP 2: NORMALIZE                                 │
│                                                   │
│  • Map each API's sportsbook names to your        │
│    sportsbooks table IDs                          │
│  • Convert all odds to both American + decimal    │
│  • Match events across APIs by name/date/sport    │
│  • Deduplicate (same event from multiple sources) │
└──────────────────────┬───────────────────────────┘
                       ▼
┌──────────────────────────────────────────────────┐
│ STEP 3: STORE                                     │
│                                                   │
│  • Upsert to odds table                           │
│  • Replace stale rows per event + book + market   │
│  • Mark odds older than 5 minutes as stale        │
└──────────────────────┬───────────────────────────┘
                       ▼
┌──────────────────────────────────────────────────┐
│ STEP 4: CALCULATE                                 │
│                                                   │
│  • Scan all active promotions                     │
│  • For each promo → find best hedge across all    │
│    current odds                                   │
│  • For pure arbs → scan all book pairs            │
│  • Calculate stakes + profit for each             │
│  • Upsert to opportunities table                  │
│  • Mark stale opportunities as expired            │
└──────────────────────┬───────────────────────────┘
                       ▼
┌──────────────────────────────────────────────────┐
│ STEP 5: GENERATE AI CARDS                         │
│  (only for new or changed opportunities)          │
│                                                   │
│  • Hash the opportunity input data                │
│  • Check Redis cache by hash                      │
│  • If cache miss → call LLM API → cache response  │
│  • Write ai_card text to opportunity row           │
└──────────────────────────────────────────────────┘
```

## Event Matching — The Hard Problem

When TheOddsAPI says "Lakers vs Celtics," Sportradar says "Los Angeles Lakers at Boston Celtics," and OddsJam says "LAL @ BOS" — you need to match these as the same event.

### Matching Strategy

```
1. Primary key: sport + date + team names (fuzzy matched)
2. Maintain a team_aliases table:
   "LAL" → "Los Angeles Lakers"
   "BOS" → "Boston Celtics"
   etc.
3. Match by:
   a. Exact normalized name match (after alias resolution)
   b. Same sport + same date + Levenshtein distance < threshold
   c. If ambiguous, prefer TheOddsAPI event_id as the canonical reference
4. Log unmatched events for manual review
```

### API-Specific Notes

**TheOddsAPI:**
- Clean REST API, consistent event naming
- Rate limit: varies by plan (500–5,000 requests/month)
- Returns American and decimal odds
- Best documentation of the three

**Sportradar:**
- Complex nested responses with their own entity IDs
- Enterprise pricing — clarify costs before committing
- Rich event metadata (scores, status, play-by-play)
- Best for live score data and event metadata

**OddsJam:**
- Designed for their own UI, not external consumption
- Good for line shopping (comparing odds across books)
- Their data model may need significant transformation
- Pricing starts at $99–199/month

## Pipeline Reliability

### Error Handling
- Each API fetch is independent — one failing doesn't block the others
- Failed fetches are logged and retried on the next cycle
- Stale odds (>5 min old) are marked and excluded from calculations
- Pipeline health exposed in the admin panel

### Vercel Cron Limitations
- Minimum interval: 1 minute (sufficient for MVP)
- Function timeout: 60 seconds on Pro plan
- **Risk:** If all 3 APIs + calculations + AI generation exceed 60 seconds, the pipeline will timeout

### Mitigation: Split Pipeline
Instead of one monolithic function, split into:

```
Cron fires every minute
  ├── /api/cron/fetch-theoddsapi     (15-20s)
  ├── /api/cron/fetch-sportradar     (15-20s)  ← run in parallel
  ├── /api/cron/fetch-oddsjam        (15-20s)
  └── /api/cron/calculate            (runs after fetches complete)
       └── /api/cron/generate-cards  (async, only for new opps)
```

This keeps each function well within the 60-second timeout.

## Scraping Pipeline (Offer Library)

Separate from the odds pipeline. Runs less frequently (every few hours or on-demand).

```
1. For each sportsbook in the sportsbooks table:
   a. Firecrawl scrapes the promo/bonus page
   b. Extract: title, value, type, wagering req, expiry, states
   c. Compare against existing promotions in DB
   d. Insert new promos, deactivate expired ones
   e. Optionally call LLM to generate ai_summary

2. Admin can manually add/edit/override any promotion
```

### Why Scraping Is Fragile
- Each sportsbook has a different page structure
- Pages are heavily JavaScript-rendered (SPAs)
- Sportsbooks change layouts frequently
- State-specific content requires geolocation or URL variations
- **Recommendation:** Launch with manual entry + scraping for 3–4 major books
