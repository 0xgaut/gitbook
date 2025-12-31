# Merchants

Merchants are permissioned entities that facilitate the minting and redemption of Universal assets. They ensure seamless liquidity movement across chains.

## Overview

Merchants play a critical role in the Universal ecosystem by providing just-in-time liquidity and executing trades. They act as the operational bridge between users and the custody layer.

## Responsibilities

### Minting & Redemption

Merchants initiate the creation and burning of Universal tokens:

- **Minting**: When users want to buy uAssets, Merchants deposit collateral with the Custodian and mint new tokens
- **Burning**: When users want to sell uAssets, Merchants burn tokens and release collateral

### Liquidity Optimization

Merchants provide assets where demand exists, reducing fragmentation:

- Monitor demand across chains
- Deploy capital efficiently using just-in-time liquidity
- Optimize pricing based on market conditions

### Market Execution

Merchants facilitate cross-chain execution through intent-based fulfillment:

- Source liquidity from multiple venues (CEXs, DEXs, OTC desks)
- Execute at competitive pricing
- Settle trades onchain

## How Merchants Work

### Merchant Workflow

1. **Collateral Deposit**: Merchant requests to mint uAssets, depositing collateral with the Custodian
2. **Verification**: The Universal protocol verifies the request and confirms collateral
3. **Minting**: The network executes a minting transaction, creating the specified number of Universal tokens
4. **Distribution**: Merchants can then distribute uAssets to users on supported chains
5. **Redemption**: When users redeem uAssets, merchants initiate burn transactions, and collateral is released back to them

### Capital Requirements

Merchants must maintain sufficient collateral with the Custodian to support minting operations:

- **1:1 backing**: Every uAsset minted requires equivalent underlying asset in custody
- **Dynamic allocation**: Merchants can adjust collateral across different assets based on demand
- **Instant minting**: Small orders execute instantly; large orders may require additional verification

## Becoming a Merchant

### Eligibility Requirements

Merchants undergo a vetting process to ensure operational and security standards:

- Institutional-grade infrastructure
- Compliance with regulatory requirements
- Demonstrated market-making expertise
- Sufficient capital to support operations

### Application Process

Interested in becoming a Merchant?

Contact the Universal partnerships team:

📩 **Email:** austin@universal.xyz  
💬 **Telegram:** @austindiamond

[View partnerships page →](../../introduction/core-contributors.md)

## Merchant Incentives

Merchants earn fees for providing liquidity:

- **Spread**: Difference between buy and sell prices
- **Execution fees**: Fees for fulfilling trades
- **Volume rebates**: Incentives for high-volume operations

## Risk Management

Merchants implement sophisticated risk management:

### Collateral Management

- Monitor collateral ratios in real-time
- Rebalance across assets and chains
- Maintain buffer for market volatility

### Price Risk

- Hedge exposure using derivatives
- Source liquidity from multiple venues
- Use dynamic pricing models

### Operational Risk

- Redundant infrastructure
- Automated monitoring and alerts
- Incident response procedures

## Current Merchants

Universal works with a select group of institutional merchants to ensure:

- Deep liquidity across all supported assets
- Competitive pricing for users
- High reliability and uptime

The merchant network will expand over time as the protocol scales.

## For Users

### What this means for you

You don't need to interact directly with Merchants. The Universal API and smart contracts handle:

- Automatic merchant selection
- Best execution pricing
- Seamless order fulfillment

### Merchant Transparency

While Merchants are permissioned, the system remains transparent:

- All mints and burns are onchain
- Reserves are publicly verifiable
- Smart contracts are open source and audited

## Technical Details

### Onchain Operations

Merchants interact with Universal smart contracts to:

```solidity
// Example: Merchant minting uAssets (conceptual)
function mint(address recipient, uint256 amount) external onlyMerchant {
    require(custodian.verifyCollateral(msg.sender, amount), "Insufficient collateral");
    _mint(recipient, amount);
    emit Minted(recipient, amount, msg.sender);
}
```

### API Integration

Merchants use internal APIs to:
- Monitor order flow
- Update pricing
- Execute fulfillment
- Report to custody systems

## Related Concepts

- [Custodians](custodians.md) - Where Merchants deposit collateral
- [Issuance](issuance.md) - The minting process in detail
- [Redemption](redemption.md) - The burning process in detail

## Questions?

- [FAQ](../../resources/support.md)
- [Discord](http://discord.gg/universalassets)
- [Contact partnerships](../../introduction/core-contributors.md)
