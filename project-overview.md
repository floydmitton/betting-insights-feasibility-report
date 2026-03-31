# Project Overview

## What The System Does

Sportsbooks spend billions on customer acquisition. They offer sign-up bonuses like **"Bet $5, Get $200 in Free Bets"** or **"$1,000 First Bet Safety Net"**. These bonuses have a real mathematical cash value that can be extracted through a technique called **matched betting** — placing calculated bets on opposing outcomes across different sportsbooks to guarantee a profit regardless of the result.

This platform **automates the discovery and instruction side** of that process. It does not place bets, hold funds, or predict winners.

## How It Works — End to End

### Step 1: Data Ingestion
Every ~60 seconds, the platform pulls real-time odds from all major US sportsbooks via three API providers (TheOddsAPI, Sportradar, OddsJam). It also catalogs active sportsbook promotions via web scraping and manual entry.

### Step 2: Opportunity Calculation
A deterministic calculation engine scans all current odds and active promotions to find:
- **Free bet conversions** — the optimal hedge to convert a free bet into cash
- **Arbitrage opportunities** — odds discrepancies between books that guarantee profit
- **Deposit match conversions** — strategies to extract cash from deposit bonus offers
- **Odds boost exploitation** — ways to profit from artificially inflated odds lines
- **Second chance bet conversions** — strategies for "bet insurance" style promos

### Step 3: AI Narration
The AI layer takes the raw calculation output (books, odds, stakes, profit) and generates plain-English instruction cards: "Open DraftKings, navigate to NBA > Lakers vs Celtics > Moneyline, place $200 on Lakers at +130..."

### Step 4: Personalized Delivery
Each subscriber's dashboard is filtered by:
- Their US state (determines which books and promos are available)
- Which sportsbook accounts they have
- Their bankroll size
- Their subscription tier (Free / Basic / Premium)

## Who This Is For

- **New sports bettors** who want to extract maximum value from sign-up bonuses
- **Experienced matched bettors** who want to save time finding opportunities
- **Bonus hunters** who systematically work through every available sportsbook promotion

## Business Model

Three-tier SaaS subscription:

| Tier | Access Level |
| --- | --- |
| **Free** | Limited opportunities per day, basic calculator, top 5 books only |
| **Basic** | All opportunities, all calculators, all books, daily email digest |
| **Premium** | Everything in Basic + real-time high-value alerts + priority access |

Revenue via Stripe Billing with monthly and annual pricing options.

## Market Validation

This is not a novel concept. Existing competitors validate the market:

| Competitor | What They Do | Pricing |
| --- | --- | --- |
| **OddsJam** | Odds comparison, arbitrage finder, positive EV tools | $99–$249/month |
| **DarkHorse Odds** | Matched betting tools, free bet converters | $49–$149/month |
| **Pikkit** | AI-powered betting insights and arbitrage | $99/month |
| **BonusFinder** | Sportsbook bonus aggregation | Free (affiliate revenue) |

The existence of these products at these price points proves willingness to pay. The opportunity is to build a better, more user-friendly version with stronger AI-driven guidance.
