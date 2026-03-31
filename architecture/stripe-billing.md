# Stripe & Billing Flow

Stripe handles all payment processing, subscription management, and billing. The platform never touches or stores raw payment information.

---

## Subscription Tiers

| Tier | Features | Suggested Pricing |
| --- | --- | --- |
| **Free** | 3–5 opportunities/day, basic calculator, top 5 books | $0 |
| **Basic** | All opportunities, all calculators, all books, daily digest | $29–49/month |
| **Premium** | Everything in Basic + real-time alerts + priority access | $79–149/month |

Annual pricing at a discount (e.g., 20% off = 2 months free).

Final pricing confirmed by client at kickoff.

## Stripe Configuration

### Products and Prices

```
Stripe Dashboard:
  Product: "Basic Plan"
    ├── Price: $49/month (recurring, monthly)
    └── Price: $470/year (recurring, annual — 20% discount)

  Product: "Premium Plan"
    ├── Price: $99/month (recurring, monthly)
    └── Price: $950/year (recurring, annual — 20% discount)
```

### Checkout Flow

```
1. User clicks "Subscribe to Basic" on Pricing page
2. Server creates Stripe Checkout Session:
   - line_items: [{ price: "price_basic_monthly", quantity: 1 }]
   - customer_email: user's email
   - metadata: { user_id: "..." }
   - success_url: /dashboard?session_id={CHECKOUT_SESSION_ID}
   - cancel_url: /pricing
3. User redirected to Stripe-hosted payment page
4. User enters card details and pays
5. Stripe processes payment
6. Stripe fires webhook: checkout.session.completed
7. User redirected to success_url
```

## Webhook Flow

### Events to Handle

| Event | Action |
| --- | --- |
| `checkout.session.completed` | Create subscription record, update user tier |
| `customer.subscription.updated` | Update plan/tier on upgrade or downgrade |
| `customer.subscription.deleted` | Revert tier to "free" |
| `invoice.payment_failed` | Set status to "past_due", optionally gate access |
| `invoice.paid` | Clear "past_due" status |

### Webhook Handler

```
POST /api/webhooks/stripe

1. Verify Stripe webhook signature (critical for security)
2. Parse event type
3. Switch on event type:

   checkout.session.completed:
     → Extract customer_id from session
     → Extract user_id from metadata
     → Create/update subscription record in Supabase
     → Update users.tier to match the plan
     → Update users.stripe_customer_id

   customer.subscription.updated:
     → Extract subscription_id
     → Look up user by stripe_customer_id
     → Update subscription record (plan, status, period_end)
     → Update users.tier

   customer.subscription.deleted:
     → Extract subscription_id
     → Look up user by stripe_customer_id
     → Update subscription status to "canceled"
     → Update users.tier = "free"
     → (takes effect at current_period_end)

   invoice.payment_failed:
     → Update subscription status to "past_due"
     → Trigger "payment failed" email via Resend
```

## Stripe Customer Portal

For managing existing subscriptions, embed Stripe's hosted Customer Portal:

```
1. User clicks "Manage Subscription" on billing page
2. Server creates Stripe Portal Session:
   - customer: user's stripe_customer_id
   - return_url: /account/billing
3. User redirected to Stripe-hosted portal
4. User can: update payment method, view invoices,
   upgrade/downgrade plan, cancel subscription
5. Any changes fire webhooks → handled as above
```

## Security Notes

- **Stripe signature verification** on every webhook — prevents spoofed events
- **No card data** ever touches the platform — full PCI compliance delegated to Stripe
- **Idempotent webhook handling** — same event processed twice produces same result
- **User tier derived from Stripe** — not from client-side state
