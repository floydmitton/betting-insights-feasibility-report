# Core Infrastructure

## Components

| Component | Purpose |
| --- | --- |
| **Secure user database** | Authentication, billing, and profile data with row-level security |
| **Structured data store** | Third-party API odds and promotional data, separate from user data |
| **3 API integrations** | TheOddsAPI (primary, 40+ US books), Sportradar (enterprise fallback, live scores), OddsJam API (secondary, line shopping) |
| **1-minute data pipeline** | Automated ingestion, normalisation, and structured storage |
| **AI integration** | LLM API for opportunity analysis, EV narration, personalised insight generation, response caching |
| **Role-based access control** | Enforced at database level, aligned to Free / Basic / Premium tiers |
| **Stripe billing** | Subscription management, webhook handling, real-time tier sync |
| **Admin panel** | User management, subscription visibility, manual offer entry, basic platform metrics |
| **Email notifications** | Transactional (account, billing) and opportunity alert emails via Resend |
| **Monitoring** | Sentry error monitoring and Vercel Analytics for performance and user behaviour |

## Scalability Design Principles

### Serverless-First
Vercel functions auto-scale with no capacity planning. Handles NFL Sunday traffic spikes without intervention or cost pre-commitment.

### AI Response Caching
LLM API outputs cached in Redis and shared across users where underlying data is identical. Keeps AI costs flat as subscribers grow.

### Single Server-Side API Polling
Odds APIs are polled every minute by a single cron job. All users are served from the database. **10,000 subscribers does not increase external API call volume.**

### Row Level Security
Supabase RLS enforces tier-gated access at the database layer — reliable and tamper-proof compared to application-level checks alone.

### Modular Data Connectors
Additional sportsbook feeds slot into the ingestion pipeline independently. Scaling to more integrations requires no core re-architecture.

## Additional Requirements

- Scalable architecture — additional API integrations add as modular connectors
- Secure billing via Stripe — industry standard, PCI-compliant
- Hosting on Vercel + Supabase — managed, globally distributed, auto-scaling
- HTTPS everywhere with automatic certificate management
