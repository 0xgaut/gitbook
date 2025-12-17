# Referral Program

Lorem ipsum dolor sit amet, consectetur adipiscing elit. Earn fees by referring traders to Universal Protocol.

## How Referrals Work

Lorem ipsum dolor sit amet, consectetur adipiscing elit. Sed do eiusmod tempor incididunt ut labore et dolore magna aliqua.

### Referral Fees

Lorem ipsum dolor sit amet:

- Earn a percentage of trading fees from your referrals
- Fees are paid in USDC
- No limit on earnings

### Fee Distribution

Lorem ipsum dolor sit amet, consectetur adipiscing elit:

1. User places trade
2. Referral fee is added to the order
3. Fees settle at the end of each month
4. USDC is distributed to referrer address

## Getting Started

### 1. Get Your Referral Link

Lorem ipsum dolor sit amet, consectetur adipiscing elit.

### 2. Share Your Link

Lorem ipsum dolor sit amet:

- Social media
- Blog posts
- Community channels
- Direct outreach

### 3. Track Earnings

Lorem ipsum dolor sit amet, consectetur adipiscing elit. Monitor your referral performance in the dashboard.

## For Developers

Lorem ipsum dolor sit amet, consectetur adipiscing elit. Integrate referral fees into your application.

### Implementation

Add `referrer_address` and `referrer_bps` parameters to quote requests:

```typescript
const quoteRequest = {
  type: "BUY",
  token: "BTC",
  pair_token: "USDC",
  blockchain: "BASE",
  slippage_bips: 20,
  user_address: "0x...",
  pair_token_amount: "5000000",
  referrer_address: "0x...",  // Your address
  referrer_bps: "10"           // 0.10% fee
};
```

### Fee Limits

Lorem ipsum dolor sit amet:

- Minimum: 0 bps
- Maximum: 100 bps (1%)
- Recommended: 10-30 bps

## Payment Schedule

Lorem ipsum dolor sit amet, consectetur adipiscing elit. Fees are settled monthly.

### Settlement Process

Lorem ipsum dolor sit amet:

1. Month ends
2. Fees calculated
3. USDC distributed within 5 business days

## Terms & Conditions

Lorem ipsum dolor sit amet, consectetur adipiscing elit. Sed do eiusmod tempor incididunt ut labore et dolore magna aliqua.

## Next Steps

- [Integrate the API](../developers/api.md)
- [Contact partnerships team](../introduction/core-contributors.md)
