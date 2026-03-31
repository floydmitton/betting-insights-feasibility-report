# User Portal (6–10 Authenticated Pages)

All portal pages sit behind authentication. Access to specific pages and data is enforced at the database level based on the user's active Stripe subscription tier.

---

## Registration & Login

- Email/password signup and Google OAuth via Azure Entra External ID (or Supabase Auth)
- Email verification flow
- Password reset via email link
- Session persistence with secure JWT tokens
- New users redirected to onboarding flow on first login

## Onboarding Flow

3-step wizard on first login:

1. **Select your US state** — determines which sportsbooks and promos are available
2. **Select sportsbook accounts** — checklist of covered books the user already has
3. **Set starting bankroll** — used to personalise stake recommendations

Data saved to user profile and used to personalise the dashboard and filter opportunities.

## Main Dashboard

The primary screen users see after login.

- Personalised opportunity feed filtered to the user's state and active sportsbook accounts
- Each opportunity displayed as an AI-generated card showing:
  - Book A and Book B
  - Sport and event
  - Market type
  - Back odds and hedge odds
  - Recommended stake amounts
  - Expected profit in USD
  - Plain-English instruction summary
- Opportunities timestamped and sorted by expected profit (highest first)
- **Free tier:** Limited number of opportunities visible; remaining ones blurred with upgrade CTA

## Opportunity Finder

Full searchable and filterable list of all current matched betting and arbitrage opportunities.

**Filters include:**
- Sport (NBA, NFL, MLB, NHL, etc.)
- Sportsbook
- Minimum profit threshold
- Bonus type (free bet, deposit match, odds boost, reload)

Each row expandable to show full AI-generated step-by-step execution instructions.

## Offer Library

Curated list of currently active sportsbook promotions in the user's state.

**Each offer shows:**
- Sportsbook name and logo
- Offer type (free bet, deposit match, odds boost, second chance, reload)
- Offer value in USD
- Wagering requirements
- Expiry date
- Estimated conversion value (how much cash the user can realistically extract)
- Difficulty rating (Easy / Medium / Hard)
- Link to the offer page on the sportsbook

**Data sourced via:**
- Firecrawl scraping of sportsbook promotional pages (primary)
- Manual admin entry (fallback and override)

## Calculator Tools

Specific calculators to be confirmed at kickoff. Indicative tools:

| Calculator | What It Does |
| --- | --- |
| **Free Bet Conversion** | Input: free bet value, back odds, hedge odds → Output: recommended stakes, guaranteed profit |
| **Arbitrage Stake Calculator** | Input: odds on both sides, total stake → Output: optimal split, guaranteed profit |
| **Expected Value Calculator** | Input: odds, probability estimate → Output: EV per bet, long-term expected profit |

Each calculator accepts user inputs and returns recommended stakes, projected profit, and a summary the user can screenshot.

## My Account & Profile

- Display name and email (read-only from auth provider)
- US state selector (updates opportunity feed when changed)
- Active sportsbook accounts checklist (updates opportunity feed)
- Starting bankroll field
- Email notification preferences:
  - Daily digest: on/off
  - High-value alert: on/off
- Account created date and current subscription tier displayed

## Billing & Subscription

- Embedded Stripe Customer Portal showing:
  - Current plan
  - Next billing date
  - Payment method on file
  - Invoice history
- Upgrade, downgrade, and cancel buttons
- Plan change takes effect at end of current billing period

---

## Developer Feasibility Notes

**Effort: MEDIUM-HIGH** — The dashboard and opportunity finder are the most complex components. The core challenge is ensuring the data pipeline feeds accurate, fresh data into the UI, and that the AI cards are correct and actionable.

**Key risks:**
- Offer Library scraping reliability (see Feasibility section)
- Ensuring RLS policies correctly gate content by tier
- Performance of the opportunity feed with many concurrent users

**Estimated time:** 2–3 weeks of development.
