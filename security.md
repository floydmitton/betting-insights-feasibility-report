# Security & Compliance

The managed-services-first architecture substantially reduces the attack surface versus self-hosted infrastructure. No custom auth code, no raw payment data, and database-level access control.

---

## Security Controls

| Control | Implementation | Notes |
| --- | --- | --- |
| **Authentication** | Azure Entra External ID (or Supabase Auth) | Enterprise-grade, SOC 2 certified, MFA supported, OIDC/OAuth2 compliant. Zero custom auth code — the largest attack surface in web apps is entirely eliminated. |
| **Billing data** | Stripe holds all card data | The platform never touches or stores raw payment information. PCI compliance is Stripe's responsibility, not the client's. |
| **Database security** | Supabase Row Level Security (RLS) | Users can only access their own data. Tier-gated content enforced at DB level — not just the UI layer. |
| **API security** | JWT validation on every request | All authenticated endpoints validate tokens on every request. No session cookies, no server-side session storage. |
| **Rate limiting** | Upstash Redis | Prevents brute-force attacks and API abuse. Configurable per endpoint. |
| **Secrets management** | Vercel environment variables | All API keys encrypted at rest. No credentials in version control, ever. |
| **HTTPS everywhere** | Vercel automatic TLS | All traffic encrypted. Automatic certificate management and renewal. All third-party API calls use TLS. |
| **Data minimisation** | By design | The platform collects only what is required to operate the service. No storage of users' sportsbook credentials or account details. |

## What's NOT Stored

The platform explicitly does **not** store:
- User passwords (handled by auth provider)
- Credit card numbers or payment details (handled by Stripe)
- Sportsbook login credentials
- Sportsbook account balances or bet history
- Personally identifiable information beyond email and display name

## OWASP Top 10 Coverage

| Vulnerability | Mitigation |
| --- | --- |
| **Injection (SQL/NoSQL)** | Supabase client uses parameterised queries; no raw SQL in application code |
| **Broken Authentication** | Delegated to Azure Entra / Supabase Auth; JWT validation on every request |
| **Sensitive Data Exposure** | No PII stored beyond email; HTTPS everywhere; secrets in env vars |
| **XML External Entities** | Not applicable (JSON-only APIs) |
| **Broken Access Control** | RLS at database level; tier checked server-side, not client-side |
| **Security Misconfiguration** | Managed services handle server config; Vercel + Supabase secure by default |
| **Cross-Site Scripting (XSS)** | React auto-escapes output; no dangerouslySetInnerHTML usage |
| **Insecure Deserialization** | Not applicable (no server-side object serialization) |
| **Using Components with Known Vulns** | Dependabot / npm audit for dependency monitoring |
| **Insufficient Logging** | Sentry for error tracking; Vercel logs for access monitoring |

## Compliance Notes

| Requirement | Status |
| --- | --- |
| **PCI DSS** | Delegated to Stripe. Platform is SAQ-A (no card data handled). |
| **GDPR / Privacy** | Minimal PII collected. Privacy policy required. Cookie consent banner recommended. |
| **21+ Age Gating** | Required by most US states for gambling-adjacent services. Must be implemented in signup flow. |
| **Responsible Gambling** | 1-800-GAMBLER messaging required in footer and relevant pages. |
| **Terms of Service** | Must clearly establish the platform as informational only, not a gambling service. |
