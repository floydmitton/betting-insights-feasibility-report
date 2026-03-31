# Development Timeline

## 6–7 Weeks — Sprint by Sprint

| Sprint | Week | What We Deliver | Client Action Required |
| --- | --- | --- | --- |
| **1** | 1 | Project scaffolding: Next.js project on Vercel, Supabase database with full schema, Stripe products/prices configured, TheOddsAPI account connected, Figma design system + all page mockups started | Provide brand assets + website copy if not already submitted |
| **2** | 2 | UI/UX designs complete and submitted for approval. Public website built and deployed to staging. Supabase Auth live (signup, login, Google OAuth, email verification, password reset). Stripe billing integrated (checkout, webhooks, customer portal, all 3 tiers). | **Approve designs** (development on portal pages waits for this). Review staging site. |
| **3** | 3 | TheOddsAPI data pipeline live and refreshing every ~1 min. Odds data flowing to Supabase for all major US sports. Signup bonus conversion engine operational — scanning all events, calculating optimal hedges, writing opportunities to database. Firecrawl scraping configured for DraftKings, FanDuel, BetMGM, Caesars. | Review data quality on staging. Flag any promos that look incorrect. |
| **4** | 4 | Full user portal built: dashboard with AI opportunity cards, offer library, free bet calculator, account page, billing page. AI integration live with validation and caching. Admin panel built: user list, offer management, platform health. | **Test the full portal on staging.** Report bugs and feedback. |
| **5** | 5–6 | QA sprint: cross-browser testing (Chrome, Firefox, Safari, Edge), responsive testing (mobile/tablet/desktop), full billing flow testing (subscribe, upgrade, downgrade, cancel, payment failure), email flow testing, data pipeline reliability validation, security review, performance optimisation. | **Final staging review and pre-launch sign-off.** |
| **6** | 6–7 | DNS cutover to custom domain, SSL live, Sentry + Vercel Analytics configured, final smoke testing, **production launch**. Full credential handover. Source code in client's GitHub. 30-day bug warranty begins. | **Approve go-live.** |

---

## What Happens at Each Milestone

### Week 2: Design Sign-Off

We submit Figma mockups for every page (public site + portal + admin). The client reviews, requests changes, and formally approves.

**Why this matters:** Portal development (Weeks 3–4) depends on approved designs. Every day of design review delay pushes the timeline back by a day.

**Our commitment:** Designs submitted by end of Week 2.\
**Client commitment:** Feedback within 2 business days. Final approval within 3 business days.

### Week 4: Staging Review

The full platform is deployed to a staging URL. The client can:
- Sign up, log in, view the dashboard
- See live opportunity cards generated from real odds data
- Test the free bet calculator
- Browse the offer library
- Subscribe via Stripe (test mode)
- Access the admin panel

**Our commitment:** Full functional staging build by end of Week 4.\
**Client commitment:** Thorough testing and bug reports within 3 business days.

### Week 6: Pre-Launch Sign-Off

All bugs from staging review are resolved. Final version deployed to staging for sign-off.

**Our commitment:** All reported bugs fixed. Platform ready for production.\
**Client commitment:** Formal sign-off that the platform is ready to go live.

### Week 7: Launch

DNS cutover, production deployment, monitoring configured, credentials handed over.

**Our commitment:** Platform live and operational.\
**Client commitment:** Confirm domain DNS access. Approve go-live.

---

## What Could Delay the Timeline

| Risk | Impact | How We Mitigate |
| --- | --- | --- |
| **Slow design approval** | Every day of delay = one day added to timeline | We submit designs early in Week 2 and schedule a review call |
| **Late website copy** | Public site can't go to staging without content | We provide a copy brief in Week 1 with a clear deadline |
| **Sportsbook page redesign during build** | Scraper for that book breaks, needs rebuild | We build manual entry as primary fallback from day 1 |
| **TheOddsAPI outage or rate limit change** | Data pipeline temporarily unavailable | Pipeline has error handling and graceful degradation |
| **Scope change requests** | New features not in MVP scope require re-estimation | Any scope additions are quoted separately and explicitly agreed before work begins |

---

## Post-Launch: 30-Day Bug Warranty

From launch day, any bugs in the delivered MVP features (as defined in the Deliverables & Acceptance Criteria section) are fixed at no additional cost.

**Starts:** Launch day\
**Ends:** 30 calendar days after launch\
**Scope:** Bugs in delivered features only — not feature requests, not scraper maintenance due to sportsbook changes, not third-party service outages

After the warranty period, ongoing development and maintenance can be covered by a separate retainer agreement.
