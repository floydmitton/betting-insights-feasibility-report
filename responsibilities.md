# Responsibilities Matrix

A clear breakdown of who does what. Every task in this project falls into one of three categories: **Whitecrow delivers**, **Client provides**, or **Client's ongoing responsibility** post-launch.

---

## Before Development Starts

These are required from the client before Sprint 1 can begin.

| # | Item | Responsible | Notes |
| --- | --- | --- | --- |
| 1 | Sign contract and pay AUD $3,000 deposit | Client | Development does not begin until deposit is received |
| 2 | Provide brand assets: logo (SVG), brand colours (hex codes), fonts | Client | Required for design phase |
| 3 | Provide website copy for all public pages (Home, How It Works, Pricing, FAQ, Contact) | Client | We build the structure; you write the words. We can provide a copy brief/outline. |
| 4 | Confirm subscription tier pricing (Free / Basic / Premium, monthly + annual) | Client | We configure Stripe with your chosen prices |
| 5 | Provide custom domain name (purchased and DNS accessible) | Client | We handle DNS configuration and SSL |
| 6 | Provide a nominated email address for contact form submissions | Client | e.g., hello@yourdomain.com |
| 7 | Confirm responsible gambling disclaimer text | Client | We provide a template; client confirms or customises |

---

## During Development

### What Whitecrow Delivers

| # | Deliverable | Detail |
| --- | --- | --- |
| 1 | **All software development** | Frontend, backend, API integrations, database, AI integration, billing, email — everything |
| 2 | **UI/UX design** | Figma mockups for all pages. Client approves designs before development proceeds. |
| 3 | **TheOddsAPI integration** | We set up the API account, build the integration, and configure the data pipeline |
| 4 | **Firecrawl scraping setup** | We build scrapers for DraftKings, FanDuel, BetMGM, and Caesars promo pages |
| 5 | **Supabase database** | We design the schema, write migrations, configure Row Level Security policies |
| 6 | **Stripe Billing configuration** | We create products, prices, checkout flows, webhook handlers, and customer portal |
| 7 | **LLM integration** | We select the model, write prompts, build the validation layer, and configure caching |
| 8 | **Vercel deployment** | We set up the project, configure CI/CD, environment variables, cron jobs, and monitoring |
| 9 | **Sentry monitoring** | We configure error tracking and verify it's capturing events |
| 10 | **Resend email setup** | We build email templates, configure sending, and verify delivery |
| 11 | **Testing** | Cross-browser testing (Chrome, Firefox, Safari, Edge), responsive testing (mobile/tablet/desktop), billing flow testing, data pipeline validation |
| 12 | **Production launch** | DNS cutover, SSL, final smoke testing, go-live |
| 13 | **Credential handover** | Full admin access to every service transferred to client |
| 14 | **Source code transfer** | Complete codebase in client's GitHub account with setup documentation |
| 15 | **30-day post-launch bug warranty** | Bugs in delivered MVP features fixed at no additional cost |

### What the Client Does During Development

| # | Responsibility | When | Why It Matters |
| --- | --- | --- | --- |
| 1 | **Review and approve UI/UX designs** | Sprint 2 (Week 2) | Development can't proceed on portal pages until designs are approved. Delays here delay the entire project. |
| 2 | **Provide website copy** | By Sprint 2 (Week 2) | Pages can't go to staging without copy. Placeholder text delays feedback cycles. |
| 3 | **Provide blog content** (if launching with blog posts) | By Sprint 5 (Week 5) | We build the blog template; you provide the posts. |
| 4 | **Test staging builds** | Sprints 3–5 | We deploy to a staging URL for your review. You test and report issues. |
| 5 | **Respond to questions within 24 hours** | Throughout | Design decisions, copy clarifications, pricing confirmations. Slow responses delay delivery. |
| 6 | **Create accounts for third-party services** (if client wants accounts in their name from day 1) | Sprint 1 | TheOddsAPI, Stripe, Supabase, Vercel, Resend, Firecrawl, GitHub. Alternatively, we create accounts and transfer later. |

---

## After Launch — Ongoing Client Responsibilities

These are the client's responsibilities once the platform is live. Whitecrow is not responsible for these unless a separate maintenance retainer is agreed.

### Monthly Service Costs (Client Pays Directly)

| Service | Estimated Cost | What It Is |
| --- | --- | --- |
| **Vercel Pro** | AUD $20–$150/month | Hosting, CDN, serverless functions |
| **Supabase Pro** | AUD $25–$100/month | Managed database |
| **TheOddsAPI** | AUD $150–$300/month | Real-time odds data feed |
| **LLM API (Claude / OpenAI)** | AUD $50–$200/month | AI instruction card generation |
| **Firecrawl** | AUD $30–$100/month | Promo page scraping |
| **Upstash Redis** | AUD $5–$20/month | Caching and rate limiting |
| **Resend** | AUD $0–$20/month | Email delivery |
| **Sentry** | AUD $0–$30/month | Error monitoring |
| **Stripe** | 2.9% + 30c per transaction | Payment processing |
| **TOTAL** | **AUD $300–$600/month** | At MVP scale |

### Operational Responsibilities

| # | Responsibility | Frequency | Notes |
| --- | --- | --- | --- |
| 1 | **Monitor platform health** via admin panel | Daily | Check that API fetches and scrapes are running. If a scraper breaks, use manual entry as fallback. |
| 2 | **Review and manage promotions** | Daily | Verify scraped promos are accurate. Manually add promos that scraping missed. Disable expired promos if auto-detection fails. |
| 3 | **Respond to user support inquiries** | As needed | Contact form submissions, billing questions, etc. |
| 4 | **Publish blog content** | As desired | SEO + organic traffic. Templates provided, content is client's responsibility. |
| 5 | **Manage Stripe billing** | As needed | Refunds, disputes, pricing changes |
| 6 | **Pay monthly service invoices** | Monthly | All third-party services billed directly to client |
| 7 | **Legal compliance** | Ongoing | Responsible gambling messaging, state-specific advertising rules, ToS updates, privacy policy |
| 8 | **Marketing and user acquisition** | Ongoing | Whitecrow does not provide marketing services unless separately engaged |

---

## What the 30-Day Bug Warranty Covers

| Covered | NOT Covered |
| --- | --- |
| Bugs in delivered MVP features (as defined in the MVP Scope section) | Feature requests or changes to functionality |
| Broken billing flows (subscribe, upgrade, downgrade, cancel) | Changes to pricing, tier definitions, or Stripe products |
| Data pipeline failures caused by our code | TheOddsAPI outages or rate limit changes |
| Scraper failures caused by our code | Sportsbook page redesigns that break scrapers (this is ongoing maintenance) |
| Email delivery failures caused by our code | Resend service outages |
| Authentication issues caused by our code | Client misconfiguration of third-party accounts |
| UI rendering bugs across tested browsers | Browser versions not included in the test matrix |
| Database/RLS policy errors | Client-modified SQL or database changes |

**After the 30-day warranty:** Ongoing development, maintenance, and scraper upkeep can be covered under a separate retainer agreement (quoted separately).

---

## Communication During Development

| Channel | Purpose |
| --- | --- |
| **Slack / Discord** (TBD) | Day-to-day questions, quick feedback |
| **Email** | Formal decisions, milestone sign-offs, contract matters |
| **Staging URL** | Client tests builds at a staging URL before production deployment |
| **Weekly check-in** (15–30 min) | Progress update, blockers, decisions needed — scheduled at kickoff |

### Milestone Sign-Offs

The client formally approves at these milestones:

| Milestone | When | What Client Approves |
| --- | --- | --- |
| **Design sign-off** | End of Week 2 | All Figma mockups for portal and public site |
| **Staging review** | End of Week 4 | Functional portal + dashboard on staging URL |
| **Pre-launch sign-off** | End of Week 6 | Full platform on staging, all features tested |
| **Launch approval** | Week 7 | Go-live on production domain |
