## Title:
ADR-3: Upgrade(buy) subscription plan

## Status:
Proposed

## Context:
The system offers some features as paid privileges and an account should buy them to enable all features.
By default, an employer has limited access to cancidate data, and he needs to use unlocks to get access to concat and resume.
Unlock is a privilege.

## Decision:
A system account can buy some privileges to use in the job market application.
The privileges are:
- unlocks
- feature jobs
- favourites
- etc

The privileges are grouped into a subscription the account can buy.

The systems support business plans:
- Promo (account does not need to pay)
- Paid (some privileges are grouped into one pack and has a price)
- Custom (account can choose required privileges). 

In custom plan each privilege has a unique price per count and the total price is calculated as
```shell
sum of all privileges(privilege_count * privilege_price_per_count)
```

Sequence diagram 
```mermaid
sequenceDiagram
    actor u as User
    participant app as Application
    participant account as Account Service
    participant billing as Billing Service
    participant stripe as Stripe
    app->>u: Request business plans
    u->>app: User selects(configure) a plan
    app->>+account: Upgrade
    account-->>account: Create a new subscription plan
    account-->>+app: [200] Created Subscription plan
    app->>u: Display created plan
    u->>app: Buy subscription plan :subscriptionPlanId
    app-->>billing: subscriptionPlanId
    billing-->>account: Retrieve subscription data by id
    account-->>billing: SubscriptionPlan
    billing-->>billing: Verify payment paramters
    billing-->>stripe: Create checkout session
    stripe-->>billing: Generated checkout page URL
    billing-->>app: Checkout page URL
    app->>stripe: Redirect to Stripe checkout
    stripe-->>stripe: Pay by card
    alt success payment
        stripe-->>billing: Success webhook
        billing-->>app: Success
        billing->>billing: Track payment
        billing->>account: Make subscription plan active
        app-->>u: Subscription plan is active
    else cancel payment
        stripe-->>+billing: Cancel webhook
        billing->>billing: Track cancel payment
        billing-->>app: Cancel
        app-->>-u: Subscription plan is inactive
    end
```
