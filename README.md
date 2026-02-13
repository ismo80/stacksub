
```markdown
# StackSub

A decentralized recurring subscription contract for the Stacks blockchain.

## Overview

StackSub is a smart contract that enables creators to manage subscription plans and handle recurring payments on the Stacks blockchain. It provides a complete subscription management system where creators can monetize their services through flexible, renewable subscription plans.

## Features

- **Create Subscription Plans**: Creators can establish custom subscription plans with configurable pricing and duration
- **Subscribe**: Users can subscribe to plans by transferring STX tokens
- **Renew Subscriptions**: Subscribers can extend their access by renewing active subscriptions
- **Cancel Subscriptions**: Subscribers maintain full control and can cancel at any time
- **Payment Tracking**: Automatic tracking of total payments and subscription periods
- **Secure Withdrawals**: Plan creators can withdraw accumulated subscription revenue
- **Query Subscriptions**: Read-only functions to check plan details and subscription status

## Contract Functions

### Public Functions

#### `create-plan`
Creates a new subscription plan.
```
(create-plan (name (string-ascii 64)) (price uint) (duration uint))
```
- **Parameters**:
  - `name`: Plan name (max 64 ASCII characters)
  - `price`: Cost in STX microunits
  - `duration`: Subscription duration in blocks
- **Returns**: Plan ID, price, and duration on success

#### `subscribe`
Subscribe to an existing plan.
```
(subscribe (plan-id uint) (amount uint))
```
- **Parameters**:
  - `plan-id`: ID of the plan to subscribe to
  - `amount`: STX amount to transfer
- **Returns**: Subscription status and expiration block
- **Transfers**: STX from subscriber to contract

#### `renew`
Renew an active subscription.
```
(renew (plan-id uint) (amount uint))
```
- **Parameters**:
  - `plan-id`: ID of the plan to renew
  - `amount`: STX amount to transfer
- **Returns**: New expiration block
- **Transfers**: STX from subscriber to contract

#### `cancel-subscription`
Cancel an active subscription.
```
(cancel-subscription (plan-id uint))
```
- **Parameters**:
  - `plan-id`: ID of the subscription plan to cancel
- **Returns**: Cancellation confirmation

#### `withdraw`
Withdraw STX from the contract (owner only).
```
(withdraw (amount uint))
```
- **Restricted**: Only contract owner can call
- **Parameters**:
  - `amount`: STX amount to withdraw

### Read-Only Functions

#### `get-plan`
Retrieve plan details.
```
(get-plan (id uint)) → (tuple)
```

#### `get-subscription`
Check subscription status for a user.
```
(get-subscription (plan-id uint) (subscriber principal)) → (tuple)
```

#### `is-active`
Verify if a subscription is currently active.
```
(is-active (plan-id uint) (subscriber principal)) → bool
```

#### `get-total-subscribers`
Get total number of active subscriptions.
```
(get-total-subscribers) → uint
```

## Data Structures

### Plans Map
```
{
  creator: principal,
  name: string-ascii 64,
  price: uint,
  duration: uint,
  active: bool
}
```

### Subscriptions Map
```
{
  start-block: uint,
  end-block: uint,
  active: bool,
  total-paid: uint
}
```

## Error Codes

| Code | Constant | Description |
|------|----------|-------------|
| 100 | ERR_NOT_OWNER | Only contract owner can perform this action |
| 101 | ERR_INVALID_AMOUNT | Invalid price, amount, or duration |
| 102 | ERR_SUBSCRIPTION_EXPIRED | Subscription has expired |
| 103 | ERR_ALREADY_ACTIVE | Subscription is already active |
| 104 | ERR_NO_SUBSCRIPTION | Plan or subscription not found |
| 105 | ERR_NOT_SUBSCRIBER | User is not a subscriber to this plan |

## Usage Example

```clarity
;; Create a subscription plan: $10/month
(contract-call? .stacksub create-plan 
  "Premium Plan" 
  u10000000  ;; 10 STX in microunits
  u4320)     ;; ~30 days in blocks

;; Subscribe to plan 1
(contract-call? .stacksub subscribe u1 u10000000)

;; Renew the subscription
(contract-call? .stacksub renew u1 u10000000)

;; Check if subscription is active
(contract-call? .stacksub is-active u1 tx-sender)

;; Cancel subscription
(contract-call? .stacksub cancel-subscription u1)
```

## Installation & Setup

1. Clone the repository
2. Install dependencies:
   ```bash
   npm install
   ```

3. Configure network settings in settings directory:
   - `Devnet.toml` - Local development
   - `Testnet.toml` - Stacks testnet
   - `Mainnet.toml` - Production

4. Deploy the contract:
   ```bash
   clarinet contract deploy
   ```

## Testing

Run the test suite:
```bash
npm run test
```

Tests are located in stacksub.test.ts and cover all contract functions.

## Contract States & Lifecycle

1. **Plan Creation**: Creator deploys plan with price and duration
2. **Subscription**: User transfers STX and becomes subscriber
3. **Active Period**: Subscriber has access during subscription window
4. **Expiration**: Subscription ends at end-block if not renewed
5. **Renewal**: Subscriber can extend by renewing before or after expiration
6. **Cancellation**: User can cancel anytime, ending access

## Security Considerations

- Only verified creators can create plans
- Subscriptions are tied to plan-id + subscriber principal pair
- STX transfers are atomic and verified
- Owner-restricted functions prevent unauthorized withdrawals
- Active flag prevents double-processing of subscriptions

## Future Enhancements

- Refund mechanism for canceled subscriptions
- Tiered pricing and discounts
- Automated renewal with stored payment method
- Subscription pause functionality
- Multi-token support
- Revenue sharing for platform operators

```

This README provides comprehensive documentation covering the contract's purpose, features, API reference, usage examples, and setup instructions suitable for both developers and users.This README provides comprehensive documentation covering the contract's purpose, features, API reference, usage examples, and setup instructions suitable for both developers and users.
