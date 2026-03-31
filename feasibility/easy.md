# What's Straightforward

These components use well-established patterns with excellent documentation. Low risk, predictable effort.

---

## Public Website — Next.js Marketing Pages

5–7 pages built with Next.js App Router, Tailwind CSS, and shadcn/ui components.

- Server-side rendering for SEO is built into Next.js
- Responsive design across mobile/tablet/desktop with Tailwind breakpoints
- SEO meta tags, Open Graph, and sitemap.xml are standard patterns
- Contact form → email via Resend is trivial

**Effort:** 3–4 days after designs are approved and client provides copy.

## Supabase Auth

Email + password signup, Google OAuth, email verification, and password reset — all handled by Supabase Auth with no custom auth code.

- Native integration with Supabase database — JWT tokens work with RLS automatically
- Google OAuth is a configuration step, not custom development
- Password reset and email verification are built-in flows

**Effort:** 1–2 days.

## Stripe Billing

One of the best-documented integrations in the ecosystem.

- Stripe Checkout creates hosted payment pages (no custom payment form)
- Stripe Customer Portal handles subscription management out of the box
- Webhook handling: well-documented, official Node.js library
- Tier sync (webhook → Supabase user.tier update) is a standard pattern

**Effort:** 2–3 days including testing all flows (subscribe, upgrade, downgrade, cancel, payment failure).

## User Account & Profile Pages

Standard CRUD operations against the users table.

- Account page: display and edit user preferences
- Notification preferences: boolean toggles → save to Supabase
- Billing page: embed Stripe Customer Portal

**Effort:** 1–2 days.

## Admin Panel

Simple internal-only area: user list with search, manual promotion entry, platform health indicators.

- Same Next.js + Supabase stack
- Protected by admin role check
- No complex UI requirements

**Effort:** 2–3 days.

## Email System

Resend SDK + React Email templates.

- Install, configure, create JSX templates, send
- 7 email types (welcome, verification, subscription events, daily digest)
- React Email provides pre-built components for responsive email layout

**Effort:** 1–2 days.

## Vercel Deployment

Near-zero configuration.

- Connect GitHub repo → automatic deploys on push
- Environment variables in Vercel dashboard
- SSL/HTTPS automatic
- Cron jobs configured in vercel.json

**Effort:** 2–3 hours.

---

## Total for Straightforward Work

**~11–17 days.** This is roughly 40% of the total MVP effort and carries minimal technical risk.
