# What's Easy (Standard Work)

These are solved problems with well-documented patterns. Low risk, predictable effort.

---

## Public Website — Next.js Marketing Pages

Standard marketing site work. Nothing unusual.

- Next.js SSR/SSG for SEO is built-in
- Tailwind CSS for responsive design across breakpoints
- Component libraries (shadcn/ui) provide pre-built UI primitives
- SEO meta tags, Open Graph, sitemap.xml are well-documented patterns
- Contact form → email is a trivial integration

**Effort:** 3–5 days after designs are approved.

## Stripe Billing + Webhooks

One of the best-documented integrations in the ecosystem.

- Stripe Checkout creates hosted payment pages (no custom form needed)
- Stripe Customer Portal handles subscription management
- Webhook handling is well-documented with official libraries
- Tier sync (webhook → Supabase update) is a standard pattern

**Effort:** 2–3 days including testing all flows (subscribe, upgrade, downgrade, cancel, payment failure).

## User Profile / Onboarding / Account Pages

Standard CRUD operations against the users table.

- Onboarding wizard: 3-step form → save to Supabase
- Account page: read/write user profile fields
- Notification preferences: boolean toggles → save to Supabase
- Nothing technically challenging

**Effort:** 2–3 days.

## Vercel Deployment + CI/CD

Near-zero configuration.

- Connect GitHub repo to Vercel
- Push to `main` → automatic production deploy
- Push to branches → automatic preview deploys
- Environment variables managed in Vercel dashboard
- SSL/HTTPS automatic

**Effort:** 1–2 hours.

## Resend Email Integration

Trivial integration.

- Install Resend SDK
- Create React Email templates (JSX → HTML email)
- Send emails via `resend.emails.send()`
- Verify domain in Resend dashboard

**Effort:** 1–2 days including all templates.

## Admin Panel (Basic Version)

A simple internal-only area with:
- User list with search
- Subscription overview / MRR counter
- Manual promotion entry form
- API health status

Using the same Next.js + Supabase stack, this is standard CRUD with no special requirements.

**Effort:** 2–3 days for a functional admin panel.

---

## Total Estimate for "Easy" Work

**~12–18 days** of development. This accounts for roughly 30–40% of the total project effort, and carries almost no technical risk.
