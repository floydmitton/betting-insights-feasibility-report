# Technology Stack

Every component is a managed service — no servers to provision, no DevOps overhead. This stack is production-grade, scales cost-effectively with subscriber growth, and keeps future upgrade paths clean.

---

## Full Stack

| Layer | Technology | Provider | Rationale |
| --- | --- | --- | --- |
| **Frontend** | Next.js 14 (React) | Vercel | SSR/SSG for SEO; edge functions; zero-config deploys |
| **Auth** | Azure Entra External ID | Microsoft Azure | SOC 2 Type II; MFA; zero custom auth code |
| **Database** | PostgreSQL + Supabase | Supabase | Managed Postgres; row-level security; real-time subscriptions |
| **Billing** | Stripe Billing | Stripe | Industry standard; webhooks drive real-time tier access control |
| **API / Backend** | Next.js API Routes / tRPC | Vercel Serverless | Type-safe; auto-scales; no server management |
| **Data Ingestion** | Vercel Cron + Edge Functions | Vercel / Supabase | 1-minute polling; lightweight; no additional infrastructure |
| **Odds Data** | TheOddsAPI | the-odds-api.com | 40+ US sportsbooks; DraftKings, FanDuel, BetMGM, Caesars+ |
| **AI Engine** | LLM API (Claude or OpenAI) | Anthropic / OpenAI | Opportunity narration, EV explanation, personalised insight cards |
| **Cache** | Upstash Redis | Upstash | Serverless Redis; AI response caching; rate limiting |
| **Email** | Resend + React Email | Resend.com | Transactional and opportunity alert email delivery |
| **Monitoring** | Sentry + Vercel Analytics | Sentry / Vercel | Error tracking; performance monitoring; user analytics |

## Why This Stack

### Next.js 14 on Vercel
- **SSR** for SEO-critical marketing pages (Google indexes server-rendered content)
- **API Routes** eliminate the need for a separate backend service
- **Edge Functions** for global low-latency responses
- **Zero-config deployment** — push to Git, Vercel deploys automatically
- The most widely adopted React framework with the largest ecosystem

### Supabase (PostgreSQL)
- **Managed PostgreSQL** — no database administration required
- **Row Level Security** — access control enforced at the database layer, not just the application
- **Real-time subscriptions** — can push data updates to the UI without polling (future use)
- **Built-in auth** — can use Supabase Auth as an alternative to Azure Entra
- **Dashboard** — visual database management for non-technical operations

### Stripe
- **Industry standard** for SaaS billing
- **Hosted Checkout** — no custom payment forms, PCI compliance delegated entirely
- **Customer Portal** — subscription management UI out of the box
- **Webhooks** — real-time event notifications for subscription lifecycle
- **2.9% + 30c per transaction** — standard, competitive pricing

### Upstash Redis
- **Serverless** — no persistent connection required (works with Vercel edge functions)
- **Pay-per-request** pricing — cost scales linearly with usage
- **Two primary uses:** AI response caching and API rate limiting

### Auth Decision: Azure Entra vs. Supabase Auth

The proposal specifies Azure Entra External ID. An alternative worth considering:

| Factor | Azure Entra External ID | Supabase Auth |
| --- | --- | --- |
| **Complexity** | Higher — separate service, more configuration | Lower — built into the database layer |
| **Google OAuth** | Supported | Supported |
| **Email/password** | Supported | Supported |
| **Integration with Supabase** | Requires custom JWT mapping | Native — RLS policies work automatically |
| **MFA** | Built-in | Built-in |
| **Cost** | Free to 50,000 MAU | Included in Supabase plan |
| **Enterprise features** | SOC 2, conditional access, audit logs | Basic audit logs |

**Recommendation:** Use **Supabase Auth** unless enterprise compliance requirements (SOC 2, conditional access) are explicitly needed. It reduces complexity significantly.

## Project File Structure

```
/
├── src/
│   ├── app/                      # Next.js App Router
│   │   ├── (public)/             # Marketing pages (no auth)
│   │   │   ├── page.tsx          # Home
│   │   │   ├── how-it-works/
│   │   │   ├── features/
│   │   │   ├── pricing/
│   │   │   ├── faq/
│   │   │   ├── blog/
│   │   │   └── contact/
│   │   ├── (portal)/             # Authenticated pages
│   │   │   ├── dashboard/
│   │   │   ├── opportunities/
│   │   │   ├── offers/
│   │   │   ├── calculators/
│   │   │   ├── account/
│   │   │   ├── billing/
│   │   │   └── onboarding/
│   │   ├── admin/                # Admin panel
│   │   └── api/                  # API routes
│   │       ├── webhooks/stripe/
│   │       ├── opportunities/
│   │       ├── offers/
│   │       ├── odds/
│   │       └── cron/pipeline/
│   ├── lib/
│   │   ├── api/                  # External API clients
│   │   │   ├── theoddsapi.ts
│   │   │   ├── sportradar.ts
│   │   │   ├── oddsjam.ts
│   │   │   └── normalize.ts
│   │   ├── engine/               # Calculation engine
│   │   │   ├── arb.ts
│   │   │   ├── free-bet.ts
│   │   │   ├── deposit-match.ts
│   │   │   ├── odds-boost.ts
│   │   │   ├── second-chance.ts
│   │   │   └── scanner.ts
│   │   ├── ai/                   # AI integration
│   │   │   ├── generate-card.ts
│   │   │   ├── prompts.ts
│   │   │   └── cache.ts
│   │   ├── supabase/             # Database clients
│   │   ├── stripe/               # Billing logic
│   │   └── auth/                 # Auth middleware
│   └── components/
│       ├── ui/                   # Shared UI (shadcn/ui)
│       ├── marketing/            # Public site components
│       ├── portal/               # Portal components
│       └── admin/                # Admin components
├── supabase/
│   ├── migrations/               # SQL migrations
│   └── seed.sql                  # Seed data
├── public/
├── .env.local
├── next.config.ts
├── tailwind.config.ts
└── package.json
```
