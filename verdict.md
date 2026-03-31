# Overall Verdict & Recommendations

## Feasibility Verdict: FEASIBLE

This project is buildable. The tech stack is modern and appropriate, the architecture is well-designed, and the market is validated by existing competitors (OddsJam at $99–249/month, DarkHorse Odds, Pikkit). The price of AUD $15,000 is competitive for this scope.

---

## Strengths

| Strength | Why It Matters |
| --- | --- |
| **Tech stack** is modern, proven, and appropriate | Lower risk of technical dead-ends; large ecosystem of tools and libraries |
| **All managed services** — no servers to maintain | Reduces operational burden post-launch; auto-scales with demand |
| **Security by delegation** (Azure/Stripe/Supabase) | Eliminates the #1 attack surface (custom auth); PCI compliance handled by Stripe |
| **AI as narration, not calculation** | Deterministic math for accuracy; AI for user experience. Right separation of concerns |
| **Fixed price** delivery | Delivery risk is on Whitecrow, not the client |
| **Validated market** | OddsJam, DarkHorse, Pikkit prove willingness to pay at $49–249/month |
| **Strong unit economics** | Break-even at ~7–20 subscribers depending on costs |

## Risks

| Risk | Severity | Mitigation |
| --- | --- | --- |
| **Web scraping legality** (Firecrawl on sportsbook sites) | HIGH | Get legal opinion before launch; lean on manual entry |
| **Scraping reliability** (pages change, extractors break) | HIGH | Manual entry as primary; scraping as supplement |
| **AI accuracy** (wrong stakes = users lose money) | HIGH | Validation layer between calc engine and AI output; template fallback |
| **Sportradar cost** not reflected in opex estimate | MEDIUM | Clarify pricing upfront or drop to 1–2 APIs |
| **Timeline is aggressive** (Sprints 3 & 4 overloaded) | MEDIUM | Descope to 1 API + manual offers for 6–7 week delivery |
| **Event matching across 3 APIs** | MEDIUM | Launch with 1 API; add others iteratively |
| **Sportsbook account restrictions** (books ban matched bettors) | MEDIUM | Market risk, not tech risk. Disclose to users in ToS |
| **No content included** (blog, FAQs, copy) | LOW | Budget for copywriting separately |
| **Single developer risk** (if Whitecrow is small) | MEDIUM | Ask about team size and contingency plans |

---

## Recommendations

### Before Signing

1. **Clarify Sportradar pricing** — is it included in $15K or a pass-through cost? Enterprise APIs can be $500–2,000+/month.
2. **Ask about team size** — is this one developer or a team? What's the contingency if someone is unavailable?
3. **Confirm payment schedule** — when is the remaining $12,000 due?
4. **Confirm IP ownership** — full source code and IP transfer upon final payment?
5. **Review examples** — ask for similar platforms Whitecrow has built.
6. **Get the 30-day bug warranty in writing** — what specifically does it cover?

### Architecture Decisions

1. **Use Supabase Auth instead of Azure Entra** — simpler, cheaper, native integration with the database. Unless enterprise compliance (SOC 2) is explicitly required.
2. **Launch with TheOddsAPI only** — it covers 40+ US sportsbooks. Add Sportradar and OddsJam post-launch.
3. **Launch with manual Offer Library** — admin enters promos. Add automated scraping iteratively.
4. **Explicitly exclude multi-leg/parlay calculations** from MVP.

### Post-Launch Priorities

| Priority | Feature | Why |
| --- | --- | --- |
| 1 | Content creation (blog, guides) | SEO + organic traffic |
| 2 | Second API integration (OddsJam) | Better odds coverage |
| 3 | Automated scraping for top 4 books | Reduce manual offer entry burden |
| 4 | Community features (Discord, leaderboards) | User retention + word-of-mouth |
| 5 | Native mobile apps | User preference |

---

## Questions to Ask Before Signing

1. What is the actual Sportradar pricing at this tier?
2. What is the Firecrawl cost and maintenance plan when scrapers break?
3. How large is the development team?
4. What happens if the 7-week timeline slips?
5. What does the 30-day bug warranty specifically cover?
6. Who owns the source code upon delivery?
7. What's the full payment schedule?
8. Can you provide examples of similar platforms built?

---

## Bottom Line

**Build it.** The market exists, the tech is proven, and the unit economics work. Descope smartly for launch (1 API, manual offers, Supabase Auth), nail the core experience (calculation engine + AI cards + clean UX), and iterate from there.

AUD $15,000 for this scope is below market rate ($25,000–$50,000+ is typical), which makes the fixed-price model attractive. The key is managing scope within that price — the recommendations above keep the build achievable within 6–7 weeks while preserving the core value proposition.
