# MVP Deliverables & Acceptance Criteria

This page defines exactly what "done" means for every component. Each deliverable has specific, testable acceptance criteria. The MVP is considered complete when all criteria below are met.

---

## 1. Odds Data Pipeline

| # | Acceptance Criteria | How We Verify |
| --- | --- | --- |
| 1.1 | Odds data from TheOddsAPI is fetched and stored in the database automatically | Check Supabase `odds` table populates without manual intervention |
| 1.2 | Data refreshes every ~1 minute during active event hours | Check timestamps on most recent rows — gap should be ≤2 minutes |
| 1.3 | All major US sports are covered (NFL, NBA, MLB, NHL, NCAAF, NCAAB, MMA at minimum) | Query distinct `sport` values in database |
| 1.4 | Moneyline, spread, and totals markets are captured | Query distinct `market` values |
| 1.5 | Odds are stored in both American and decimal format | Check both `price` and `decimal_price` columns are populated |
| 1.6 | Stale odds (>5 min old) are excluded from opportunity calculations | Run calculation after pausing pipeline — old odds should not generate opportunities |
| 1.7 | Pipeline errors are logged to Sentry | Simulate API failure — verify error appears in Sentry dashboard |
| 1.8 | Admin panel shows last successful fetch timestamp | Check admin health page |

---

## 2. Sportsbook Promo Scraping

| # | Acceptance Criteria | How We Verify |
| --- | --- | --- |
| 2.1 | DraftKings sign-up promo page is scraped and structured data is stored | Check `promotions` table contains DraftKings entries with correct fields |
| 2.2 | FanDuel sign-up promo page is scraped and structured data is stored | Same as above for FanDuel |
| 2.3 | BetMGM sign-up promo page is scraped and structured data is stored | Same as above for BetMGM |
| 2.4 | Caesars sign-up promo page is scraped and structured data is stored | Same as above for Caesars |
| 2.5 | Each scraped promo includes: title, value, type, wagering requirements, expiry, eligible states | Check all fields populated in database |
| 2.6 | Scraping runs automatically every 6 hours | Check timestamps over 24 hours — should see 4 scrape cycles |
| 2.7 | Admin can manually add a promotion via the admin panel | Create a test promo in admin, verify it appears in the offer library |
| 2.8 | Admin can edit or disable a scraped promotion | Modify and disable a promo in admin, verify changes reflected |
| 2.9 | Expired promos are automatically marked inactive | Set a promo expiry to yesterday, verify it stops appearing |

---

## 3. Signup Bonus Conversion Engine

| # | Acceptance Criteria | How We Verify |
| --- | --- | --- |
| 3.1 | For each active sign-up free bet, the engine identifies the best hedge across all available sportsbooks | Check `opportunities` table populates for each active promo |
| 3.2 | Each opportunity includes: back book, hedge book, event, market, both odds, both stakes, guaranteed profit, conversion rate | Check all fields populated |
| 3.3 | Profit calculation is mathematically correct for free bet conversion (free bets do not return stake) | Manually verify 5 opportunities against known-correct formula |
| 3.4 | Minimum odds requirements on promos are respected (hedges below min odds are excluded) | Set a promo with min odds of -200, verify no opportunities use odds below -200 |
| 3.5 | Opportunities are sorted by expected profit (highest first) | Check ordering on dashboard |
| 3.6 | Opportunities expire when underlying odds change significantly or event passes | Check that old events don't persist as active opportunities |
| 3.7 | Calculation engine runs on every pipeline cycle (~1 min) | Check `created_at` timestamps on recent opportunities |

---

## 4. AI Instruction Cards

| # | Acceptance Criteria | How We Verify |
| --- | --- | --- |
| 4.1 | Every active opportunity has an AI-generated instruction card | Check `ai_card` field is populated for all active opportunities |
| 4.2 | Each card includes numbered steps with specific book names, navigation path, and exact amounts | Read 10 random cards, verify all contain actionable step-by-step instructions |
| 4.3 | All numbers in the AI card exactly match the calculation engine output (no rounding, no hallucination) | Compare ai_card text against source data for 10 random opportunities |
| 4.4 | Cards are cached — identical opportunities serve from cache without calling the LLM | Check Redis cache hit rate in logs |
| 4.5 | When odds change (new calculation), a new card is generated | Force an odds change, verify card text updates |
| 4.6 | If AI validation fails, a deterministic template card is served instead | Simulate AI failure, verify fallback card appears |

---

## 5. Public Website

| # | Acceptance Criteria | How We Verify |
| --- | --- | --- |
| 5.1 | Home page loads with hero, how-it-works summary, features, and CTAs | Visual inspection across mobile (375px), tablet (768px), desktop (1280px) |
| 5.2 | How It Works page includes full matched betting explanation and worked example | Read page, verify explanation is complete and example uses real numbers |
| 5.3 | Pricing page shows 3 tiers with correct features per tier | Visual inspection, verify feature lists match specification |
| 5.4 | Pricing page monthly/annual toggle works and shows correct prices | Click toggle, verify prices update |
| 5.5 | Each tier's CTA routes to the correct Stripe Checkout | Click each CTA, verify correct Stripe product/price loads |
| 5.6 | FAQ page contains 15+ questions with working accordions | Click each accordion, verify content displays |
| 5.7 | Blog page loads with category filter and post template renders correctly | Navigate to blog, verify filter and template |
| 5.8 | Contact form sends email to nominated address | Submit form, verify email received |
| 5.9 | All pages have unique meta title, meta description, and Open Graph tags | Inspect page source / use SEO checker tool |
| 5.10 | Site is fully responsive across mobile, tablet, and desktop | Test all pages at 375px, 768px, 1280px |

---

## 6. User Portal

| # | Acceptance Criteria | How We Verify |
| --- | --- | --- |
| 6.1 | User can sign up with email + password | Complete signup flow |
| 6.2 | User can sign up / log in with Google OAuth | Complete Google OAuth flow |
| 6.3 | Email verification is sent and works | Check email received, click link, verify account activated |
| 6.4 | Password reset flow works | Request reset, receive email, set new password, log in |
| 6.5 | Dashboard shows current opportunities as AI-generated cards | Log in, verify opportunity cards display |
| 6.6 | Free tier sees 3 opportunities; remaining are blurred with upgrade CTA | Log in as free user, count visible vs blurred cards |
| 6.7 | Basic/Premium tier sees all opportunities | Log in as paid user, verify all cards visible |
| 6.8 | Offer Library shows active promos from all 4 scraped books | Navigate to offer library, verify promos from DK, FD, BetMGM, Caesars |
| 6.9 | Free Bet Calculator accepts inputs and returns correct outputs | Enter known values, verify output matches manual calculation |
| 6.10 | My Account page displays user info and notification preferences | Navigate to account, verify fields display and save |
| 6.11 | Billing page opens Stripe Customer Portal | Click manage subscription, verify Stripe portal loads |

---

## 7. Admin Panel

| # | Acceptance Criteria | How We Verify |
| --- | --- | --- |
| 7.1 | Admin can view list of all registered users with search by email | Search for a test user |
| 7.2 | Admin can see each user's tier and subscription status | Check user detail view |
| 7.3 | Admin can add a new promotion manually | Add a test promo, verify it appears in offer library |
| 7.4 | Admin can edit and disable existing promotions | Edit and disable test promos |
| 7.5 | Admin can see last successful API fetch and scrape timestamps | Check health dashboard |
| 7.6 | Admin can see total users and users per tier | Check metrics display |

---

## 8. Billing System

| # | Acceptance Criteria | How We Verify |
| --- | --- | --- |
| 8.1 | Free user can subscribe to Basic via Stripe Checkout | Complete checkout flow |
| 8.2 | Free user can subscribe to Premium via Stripe Checkout | Complete checkout flow |
| 8.3 | After payment, user's tier updates immediately (no page refresh needed beyond redirect) | Subscribe, verify dashboard shows full access |
| 8.4 | User can upgrade from Basic to Premium | Upgrade via Stripe portal, verify tier changes |
| 8.5 | User can downgrade from Premium to Basic | Downgrade, verify tier changes at period end |
| 8.6 | User can cancel subscription | Cancel, verify tier reverts to free at period end |
| 8.7 | Failed payment sets user to past_due status | Simulate failed payment via Stripe test mode |
| 8.8 | Tier gating is enforced at database level (not just UI) | Attempt to query premium data as free user via API — verify rejection |

---

## 9. Email System

| # | Acceptance Criteria | How We Verify |
| --- | --- | --- |
| 9.1 | Welcome email sends on signup | Sign up, check inbox |
| 9.2 | Subscription confirmed email sends on successful payment | Subscribe, check inbox |
| 9.3 | Payment failed email sends on Stripe payment failure | Simulate failure, check inbox |
| 9.4 | Cancellation email sends when user cancels | Cancel, check inbox |
| 9.5 | Daily digest email sends to Basic+ users with top 5 opportunities | Wait for cron cycle, check inbox for digest |
| 9.6 | All emails render correctly on mobile and desktop email clients | Test with Litmus or manual inspection |

---

## 10. Deployment & Handover

| # | Acceptance Criteria | How We Verify |
| --- | --- | --- |
| 10.1 | Site is live on client's custom domain with HTTPS | Navigate to domain, verify SSL padlock |
| 10.2 | Sentry error monitoring is active and receiving events | Trigger a test error, verify it appears in Sentry |
| 10.3 | Vercel Analytics is tracking page views | Check Vercel dashboard for analytics data |
| 10.4 | Client has admin access to: Vercel, Supabase, Stripe, Resend, TheOddsAPI, Firecrawl, GitHub, LLM API | Client logs into each service and confirms access |
| 10.5 | Source code is in client's GitHub account | Client verifies repo exists and is accessible |
| 10.6 | README in repo documents: how to run locally, environment variables, deployment process | Read README, follow setup instructions |
