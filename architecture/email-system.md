# Email System

All emails sent via **Resend** with **React Email** templates. Covers transactional emails (account management) and opportunity alerts.

---

## Email Types

### Transactional Emails

| Email | Trigger | Content |
| --- | --- | --- |
| **Welcome** | User completes signup | Onboarding instructions, link to complete profile |
| **Email Verification** | User registers with email/password | Verification link (via auth provider) |
| **Password Reset** | User requests reset | Secure reset link (via auth provider) |
| **Subscription Confirmed** | Stripe `checkout.session.completed` | Plan details, what's now unlocked, link to dashboard |
| **Payment Failed** | Stripe `invoice.payment_failed` | Warning, link to update payment method |
| **Subscription Canceled** | Stripe `customer.subscription.deleted` | What they'll lose access to, re-subscribe CTA |

### Opportunity Alert Emails

| Email | Trigger | Tiers | Content |
| --- | --- | --- | --- |
| **Daily Digest** | Cron job (morning, e.g., 8 AM ET) | Basic + Premium | Top 5 opportunities for their state/books, sorted by profit |
| **High-Value Alert** | Pipeline finds opportunity above user's threshold | Premium only | Single opportunity card with "act fast" urgency |

## Implementation

### Resend Setup

```
1. Create Resend account
2. Verify sending domain (e.g., alerts@yourdomain.com)
3. Install: npm install resend @react-email/components
4. Configure API key in Vercel environment variables
```

### Daily Digest Cron

```
Vercel Cron: 0 12 * * * (8 AM ET = 12:00 UTC)

1. Query all users where:
   - tier IN ('basic', 'premium')
   - notification_preferences.daily_digest = true
2. For each user:
   a. Get top 5 active opportunities filtered by their state + books
   b. Render React Email template with opportunity data
   c. Send via Resend API
3. Log delivery status
```

### High-Value Alert

```
Triggered during pipeline calculation step:

1. New opportunity created with expected_profit > threshold
2. Query all premium users where:
   - notification_preferences.high_value_alerts = true
   - notification_preferences.min_alert_profit <= opportunity.expected_profit
   - User's state is in opportunity.states
   - User has both sportsbooks in their active books
3. Send alert email with the single opportunity card
4. Rate limit: max 3 alerts per user per day (prevent spam)
```

## Email Template Structure

Using React Email for type-safe, reusable email templates:

```
src/emails/
  ├── welcome.tsx
  ├── subscription-confirmed.tsx
  ├── payment-failed.tsx
  ├── subscription-canceled.tsx
  ├── daily-digest.tsx
  └── high-value-alert.tsx
```

Each template receives typed props (user data, opportunity data) and renders responsive HTML email.
