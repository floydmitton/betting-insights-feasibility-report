# What's Genuinely Hard

These are the components that will consume the most time, carry the highest risk, and are most likely to cause timeline slippage.

---

## The Offer Library (Firecrawl Scraping)

**This is the single hardest feature in the entire build** from a maintenance perspective.

### Why It's Brutal

1. **Every sportsbook has a different promo page structure** — DraftKings, FanDuel, BetMGM, Caesars, ESPN BET, etc. That's 10+ different page layouts minimum.

2. **Promo pages are heavily JavaScript-rendered** (React/Angular SPAs). Firecrawl handles this, but extraction accuracy varies wildly between sites.

3. **You need to extract structured data from unstructured marketing copy.** "Get a $1,000 first bet safety net!" — what's the actual wagering requirement? It's often buried in fine print on a separate T&C page.

4. **Sportsbooks change their promo page structure constantly** — new campaigns, redesigns, seasonal layouts. Your scrapers will break regularly and without warning.

5. **State-specific offers** mean the same sportsbook shows different promos depending on geolocation or URL path.

### What Could Go Wrong

| Failure Mode | Impact | Likelihood |
| --- | --- | --- |
| Scrapers break silently, serving stale/wrong promo data | Users act on incorrect information | HIGH |
| Parse "$1,000 bonus" when it's actually a "$1,000 second chance bet" | Completely different value and conversion strategy | HIGH |
| Sportsbooks block scraper IPs / Firecrawl's IPs | Entire data source goes offline | MEDIUM |
| Legal pushback from sportsbooks | May need to shut down scraping entirely | MEDIUM |

### Realistic Assessment

Getting scraping working **reliably for 3–4 major books** (DraftKings, FanDuel, BetMGM, Caesars) is doable for MVP. Getting it reliable across 10+ books with accurate structured extraction is an **ongoing maintenance burden**, not a build-once feature.

### Recommendation

**Launch with a manual/semi-manual Offer Library.** Admin enters promos manually, supplemented by scraping where it works. The proposal already mentions "manual admin override" — lean into that for launch and build out automated scraping iteratively.

**Effort:** Manual library: 2–3 days. Full scraping for 4 books: 5–8 days + ongoing maintenance. Full scraping for 10+ books: 2–3 weeks + significant ongoing maintenance.

---

## AI Opportunity Cards — Getting Them Correct

The architecture is right (calc engine → structured JSON → AI narration → cache). But quality at scale is the challenge.

### Prompt Engineering at Scale

- You need the AI to generate **accurate, specific instructions** without hallucinating details
- The AI must **never contradict the calculation engine** — if the calc says $148.15, the card must say $148.15, not $150
- Different opportunity types need different prompt templates (free bet, arb, odds boost, deposit match, second chance)
- Tone and reading level must be consistent across thousands of generated cards

### What Could Go Wrong

| Failure Mode | Impact | Likelihood |
| --- | --- | --- |
| AI hallucinates a sportsbook name, market, or stake amount | User follows wrong instructions, loses money | MEDIUM |
| AI gives instructions for wrong book UI (e.g., "click Moneyline tab" when that book calls it "Money Line") | User confused, support burden | MEDIUM |
| AI rounds or approximates numbers instead of using exact values | Stake calculations off, user under/over-hedges | HIGH |
| Cached AI responses become stale when odds move | User acts on outdated odds | MEDIUM |

### Mitigation

1. **Validation layer:** Parse AI output and verify every number matches the input data. Reject and regenerate if any mismatch.
2. **Template fallback:** If AI fails validation 3 times, serve a deterministic template (no AI) instead.
3. **Short cache TTLs:** 2–3 minutes for odds-dependent cards.
4. **Narrow prompts:** Don't give the model freedom to improvise numbers. Pass structured data and constrain the output format.

### Effort

Integration + prompt engineering + validation + caching: **5–8 days.**

The initial integration is fast (1–2 days). Getting the prompts right and building the validation layer is where the time goes.

---

## Event Matching Across Multiple APIs

If you use more than one odds API (which the proposal requires with three), you must match events across providers.

### Why It's Hard

- No universal event ID standard exists
- Team names vary: "LA Lakers" vs "Los Angeles Lakers" vs "LAL"
- Event formats vary: "Lakers vs Celtics" vs "Lakers at Celtics" vs "BOS-LAL"
- Postponed, rescheduled, or renamed events create edge cases
- International events and lesser-known leagues have inconsistent naming

### Approaches

| Approach | Accuracy | Effort |
| --- | --- | --- |
| **Exact string match** after normalisation | ~70% | Low |
| **Fuzzy match** (Levenshtein + date + sport) | ~90% | Medium |
| **Team alias table** + fuzzy match | ~95% | Medium-High |
| **Machine learning entity resolution** | ~99% | High (overkill for MVP) |

### Recommendation

Use a **team alias table** (maintained manually) + **fuzzy matching** by sport + date + normalised names. Accept ~95% accuracy at launch and log unmatched events for manual review.

**Effort:** 3–5 days for robust matching logic + initial alias table.
