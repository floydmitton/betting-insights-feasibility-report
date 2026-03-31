# System Architecture

This section covers the complete technical architecture of the platform — how every component connects, what data flows where, and how the system operates minute-to-minute.

## Architecture Principles

1. **Managed services only** — no servers to provision, no DevOps overhead
2. **Serverless-first** — auto-scales with demand, pay only for what you use
3. **Database-level security** — RLS policies enforce access, not just UI hiding
4. **Cache aggressively** — AI and API responses cached to control costs
5. **Modular data connectors** — new API sources plug in without re-architecture

## Component Overview

```
External APIs                    User-Facing
─────────────                    ───────────
TheOddsAPI  ──┐                  ┌── Public Website (Next.js)
Sportradar  ──┼── Data Pipeline ─┼── User Portal (authenticated)
OddsJam     ──┤   (Vercel Cron)  ├── Admin Panel
Firecrawl   ──┘        │         └── Email Alerts (Resend)
                        │
                        ▼
                   ┌─────────┐
                   │Supabase │◄── Stripe Webhooks
                   │PostgreSQL│
                   └────┬────┘
                        │
                   ┌────┴────┐
                   │  Redis  │◄── AI Response Cache
                   │(Upstash)│
                   └─────────┘
```

## Key Data Flows

| Flow | Path | Frequency |
| --- | --- | --- |
| Odds ingestion | APIs → Pipeline → Supabase | Every ~1 minute |
| Opportunity calculation | Supabase odds + promos → Calc Engine → Supabase opportunities | Every ~1 minute |
| AI card generation | New opportunity → LLM API → Redis cache → Supabase | On new/changed opportunities |
| User dashboard load | Browser → Next.js API → Supabase (RLS-filtered) → Browser | On page load |
| Subscription change | Stripe → Webhook → Supabase tier update → RLS auto-adjusts | On payment events |
| Email alerts | Cron → Supabase query → Resend API | Daily + real-time for high-value |
