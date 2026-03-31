# What's Genuinely Hard

One component in the MVP carries meaningful technical risk: **sportsbook promo scraping**. Everything else is manageable with standard engineering practices.

---

## Sportsbook Promo Scraping (Firecrawl)

This is the highest-risk component in the MVP — not because it's hard to build initially, but because it's hard to **keep working**.

### Why It's Hard

**Each sportsbook is a separate engineering problem:**
- DraftKings, FanDuel, BetMGM, and Caesars each have completely different page structures
- Each requires custom extraction logic to parse the promo title, value, wagering requirements, expiry, and eligible states
- Promo pages are heavily JavaScript-rendered (React/Angular SPAs) — Firecrawl handles rendering, but the DOM structure varies wildly

**Sportsbook pages change without warning:**
- New promotional campaigns → new page layouts
- Seasonal redesigns (NFL season, March Madness, etc.)
- A/B tests that change the page structure for some visitors
- State-specific content variations

**Extracting structured data from marketing copy is inherently ambiguous:**
- "Get a $1,000 first bet safety net!" — is this a free bet? A second chance bet? What are the wagering requirements? The answer is usually in fine print on a separate T&C page.
- "$200 in bonus bets" vs "$200 first bet insurance" vs "$200 deposit match" — each is a fundamentally different promo type requiring different conversion math.

### What Could Go Wrong

| Failure Mode | Likelihood | Impact | Mitigation |
| --- | --- | --- | --- |
| Scraper breaks silently, serves stale/wrong promo data | HIGH (will happen eventually) | Users see outdated offers | Admin panel shows last-scrape timestamp; manual entry as fallback |
| Misclassifies promo type (e.g., "second chance" parsed as "free bet") | MEDIUM | Wrong conversion strategy recommended | MVP only handles signup free bets — simpler classification |
| Sportsbook blocks scraper IPs | MEDIUM | That book's promos stop updating | Manual entry fallback |
| Legal pushback from sportsbook | LOW-MEDIUM | May need to stop scraping that book | Manual entry fallback; legal review recommended pre-launch |

### Our Approach

1. **Build scrapers for 4 books at launch.** Test and validate extraction accuracy.
2. **Build manual admin entry as a first-class feature, not an afterthought.** This is the reliable fallback that works regardless of scraper status.
3. **Scraping refresh every 6 hours.** Promos change slowly (days, not minutes), so frequent scraping isn't needed.
4. **Admin health dashboard** shows last successful scrape per book. If a scraper fails, the admin knows immediately and can enter promos manually.
5. **Ongoing scraper maintenance is out of scope** for the MVP fixed price. When sportsbooks redesign their pages (and they will), fixing scrapers is either covered by a maintenance retainer or handled via manual entry.

### Honest Assessment

Scrapers will work at launch. They will break at some point after launch. The manual entry fallback ensures the platform keeps working regardless. This is the right tradeoff for an MVP.

---

## Things That Are NOT Hard (But Might Look Hard)

| Component | Why It's Manageable |
| --- | --- |
| **Real-time odds pipeline (1-min cron)** | Single API, clean docs. Split into parallel functions to stay within timeout. Standard pattern. |
| **Signup bonus conversion math** | One formula with a few edge cases. Not multiple calculation types. Well within scope. |
| **AI instruction generation** | Narration over pre-computed data, not open-ended generation. Validation + caching makes it predictable. |
| **3-tier subscription gating** | Supabase RLS + Stripe webhooks. Battle-tested pattern used by thousands of SaaS apps. |
| **Responsive portal UI** | Next.js + Tailwind + shadcn/ui. Standard React development. No unusual UI requirements. |
