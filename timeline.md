# Development Timeline

## Proposed Timeline: 5–7 Weeks (6 Sprints)

| Sprint | Week | Deliverables | Milestone |
| --- | --- | --- | --- |
| **1** | 1 | Architecture finalised, Supabase schema live, API accounts provisioned, Vercel project setup, Figma design system and all screens begun | Architecture sign-off |
| **2** | 2 | UI/UX designs approved, public website live on staging, auth live, Stripe billing with all 3 tiers integrated, DB-level tier access control | Design + auth sign-off |
| **3** | 3 | Data pipeline live, all 3 API integrations normalised and flowing to Supabase, arb/EV calculation engine operational, data quality validated | Data pipeline live |
| **4** | 4 | LLM API integration and AI opportunity cards live, full user portal built (dashboard, finder, offer library, calculators, profile, onboarding), admin panel | Beta ready |
| **5** | 5 | QA, cross-browser testing, billing flow validation, email flows end-to-end, performance optimisation, security review, staging sign-off | QA complete |
| **6** | 6 | DNS cutover to custom domain, SSL live, Sentry and analytics configured, full credential handover, production launch | Production launch |

---

## Feasibility Assessment Per Sprint

### Sprint 1 (Week 1) — Architecture + Setup ✅ FEASIBLE

- Standard project scaffolding
- Database schema is well-defined (see Database Schema section)
- API account provisioning is admin work, not development
- Design system can begin in parallel

**Verdict:** Achievable in 1 week.

### Sprint 2 (Week 2) — Design + Auth + Billing ⚠️ TIGHT

- Design approval in 1 week requires a responsive client — if design review takes 3+ days, development stalls
- Auth integration (Azure Entra) is 4–6 days of work. With Supabase Auth, it's 2–3 days.
- Stripe billing (products, checkout, webhooks, portal) is 2–3 days
- Public website requires approved designs first

**Verdict:** Achievable IF using Supabase Auth and designs are approved quickly. Tight with Azure Entra.

### Sprint 3 (Week 3) — Data Pipeline + 3 APIs ❌ OVERLOADED

- TheOddsAPI integration: 2–3 days
- Sportradar integration: 3–4 days (complex responses)
- OddsJam integration: 2–3 days
- Event matching / normalisation across all three: 3–5 days
- Calculation engine: 5–8 days
- **Total: 15–23 days of work in a 5-day sprint**

**Verdict:** Not achievable as specified. Realistic options:
- Ship with TheOddsAPI only (5–7 days) — **recommended**
- Extend to 2–3 weeks for all three APIs

### Sprint 4 (Week 4) — Portal + AI + Admin ❌ OVERLOADED

- 6–10 portal pages with complex components: 8–12 days
- AI integration + prompt engineering + validation: 5–8 days
- Admin panel: 2–3 days
- **Total: 15–23 days of work in a 5-day sprint**

**Verdict:** Not achievable as specified. This sprint needs 2–3 weeks.

### Sprint 5 (Week 5) — QA ✅ FEASIBLE

- Cross-browser testing
- Billing flow validation (all upgrade/downgrade/cancel paths)
- Email flow testing
- Performance optimisation
- Security review

**Verdict:** 1 week is sufficient for focused QA on an MVP.

### Sprint 6 (Week 6) — Launch ✅ FEASIBLE

- DNS cutover, SSL, monitoring setup
- Credential handover
- Documentation

**Verdict:** Standard launch activities, achievable in a few days.

---

## Realistic Timeline

### Option A: Full Scope (8–10 weeks)

| Week | Focus |
| --- | --- |
| 1 | Architecture, schema, setup, design system |
| 2 | Design approval, auth integration, Stripe billing |
| 3 | TheOddsAPI integration, data pipeline, start calc engine |
| 4 | Sportradar + OddsJam integration, event matching, complete calc engine |
| 5 | User portal (dashboard, finder, offer library) |
| 6 | Portal (calculators, account, billing), AI integration, admin panel |
| 7 | AI prompt engineering, email system, Offer Library scraping |
| 8 | QA, testing, performance |
| 9 | Bug fixes, staging sign-off |
| 10 | Production launch + handover |

### Option B: Descoped MVP (6–7 weeks) — RECOMMENDED

| Week | Focus |
| --- | --- |
| 1 | Architecture, schema, setup, design system |
| 2 | Design approval, Supabase Auth, Stripe billing, public website |
| 3 | TheOddsAPI integration, data pipeline, calculation engine |
| 4 | User portal (dashboard, finder, calculators, account) |
| 5 | AI integration, manual Offer Library, email system, admin panel |
| 6 | QA, testing, bug fixes |
| 7 | Production launch + handover |

**Descoped items added post-launch:**
- Sportradar + OddsJam integrations
- Automated Offer Library scraping
- Advanced admin panel features
- Multi-leg/parlay calculations
