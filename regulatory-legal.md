# Regulatory & Legal Analysis

> **Disclaimer:** This is not legal advice. Engaging a US gambling law specialist before launch is strongly recommended.

## Legal Classification

The platform operates as an **information and analytics service** — the same legal category as OddsJam, Action Network, and Covers.com. It does not accept bets, hold user funds, or facilitate wagering directly. **No US gaming licence is required** for a platform of this type.

## Requirements

| Requirement | Detail | Status |
| --- | --- | --- |
| No fund handling | Platform must never accept, process, or hold user gambling funds | ✅ By design |
| Analytical/educational only | All tools are informational — users place their own bets independently | ✅ By design |
| Age gating | Prominent 21+ age verification and 1-800-GAMBLER messaging required in most states | ✅ Planned |
| Terms of Service | Clear ToS establishing the service as informational only, not gambling | ✅ Planned |
| Licensed data sources | All odds data from licensed APIs (TheOddsAPI, Sportradar, OddsJam) — no direct sportsbook scraping for odds | ✅ Planned |
| Affiliate compliance | Sportsbook affiliate links must comply with each operator's affiliate terms | ⚠️ Needs review |

## Risk Areas

### Web Scraping of Promotional Pages — HIGH RISK

The Offer Library component uses **Firecrawl to scrape sportsbook promotional pages** for bonus offer details (values, wagering requirements, expiry dates, etc.). This is the single biggest legal risk in the project.

**Why it's risky:**
- Most sportsbook Terms of Service explicitly restrict scraping
- Sportsbooks have sent cease-and-desist letters to scrapers in the past
- The Computer Fraud and Abuse Act (CFAA) has been used against scrapers in commercial contexts
- Even if technically legal, sportsbooks can block access and revoke affiliate partnerships

**Mitigation options:**
1. Seek legal confirmation that scraping promotional content is permissible under applicable affiliate and ToS agreements before launch
2. Rely on manual entry for the Offer Library at launch, supplemented by scraping only where explicitly permitted
3. Partner with sportsbooks directly via affiliate programs that may provide promotional data feeds

### State-by-State Advertising Compliance — MEDIUM RISK

Some states have specific rules about how betting-adjacent products can be marketed:
- **New York** — strict advertising regulations for gambling-related services
- **Massachusetts** — prohibitions on certain promotional language
- **Colorado, Virginia, others** — varying requirements for responsible gambling messaging in ads

**Recommendation:** Before running paid marketing, review advertising requirements in each target state.

### FTC Affiliate Disclosure — LOW RISK

If the platform uses sportsbook affiliate links (earning commission when users sign up through the platform), FTC guidelines require clear disclosure of the affiliate relationship.

**Mitigation:** Add standard affiliate disclosure language to the site footer and relevant pages.

## Summary

The core platform — an odds comparison and analysis tool — is **clearly legal** and well-established in the market. The two areas requiring pre-launch legal review are:

1. **Scraping sportsbook promo pages** — get a legal opinion before relying on this
2. **State-specific advertising rules** — review before running paid acquisition

**Estimated legal review cost:** USD $2,000–$5,000 with a US gambling law specialist.
