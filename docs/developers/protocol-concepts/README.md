# Protocol Concepts

Deep dive into the core concepts that power Universal Protocol's wrapped asset infrastructure.

## Overview

Universal Protocol operates through a network of permissioned entities, smart contracts, and custody arrangements that enable secure, transparent cross-chain asset trading.

## Core Concepts

### Key Roles

<table>
  <tr>
    <td><strong><a href="merchants.md">Merchants</a></strong><br/>Permissioned entities that mint and burn uAssets, providing just-in-time liquidity</td>
  </tr>
  <tr>
    <td><strong><a href="custodians.md">Custodians</a></strong><br/>Secure storage providers for underlying assets backing uAssets</td>
  </tr>
</table>

### Core Processes

<table>
  <tr>
    <td><strong><a href="issuance.md">Issuance</a></strong><br/>How new uAssets are minted into circulation</td>
  </tr>
  <tr>
    <td><strong><a href="redemption.md">Redemption</a></strong><br/>How uAssets are burned and underlying assets released</td>
  </tr>
</table>

### Trust & Verification

<table>
  <tr>
    <td><strong><a href="reserves.md">Proof of Reserves</a></strong><br/>Zero-knowledge proof system for trustless reserve verification</td>
  </tr>
</table>

## Understanding the System

### 1. Actors in the Ecosystem

**Merchants** initiate minting and burning of uAssets by managing collateral with Custodians. They provide liquidity when users want to trade.

**Custodians** securely hold the underlying crypto assets that back every uAsset, ensuring 1:1 backing at all times.

**Users** trade, hold, and use uAssets in DeFi protocols without worrying about underlying custody or cross-chain complexity.

### 2. Asset Lifecycle

```
Native Asset → Custodian → Mint uAsset → User → DeFi Protocol
                                   ↓
Native Asset ← Custodian ← Burn uAsset ← User
```

### 3. Trust Model

Universal combines:
- **Permissioned actors** (Merchants, Custodians) for operational security
- **Transparent reserves** verifiable via zkProofs
- **Audited smart contracts** for execution integrity
- **Regulated custody** (Coinbase Prime) for asset security

## Design Principles

### 1. Capital Efficiency

Just-in-time liquidity means capital is deployed only when needed, unlike traditional AMM pools where liquidity sits idle.

### 2. Cross-Chain Unification

A single reserve pool backs uAssets across all chains, eliminating liquidity fragmentation.

### 3. Verifiable Security

zkProof-based reserve verification allows anyone to confirm backing without trusting Universal or revealing sensitive data.

### 4. DeFi Composability

Standard token formats (ERC-20, SPL) ensure uAssets work seamlessly with existing DeFi infrastructure.

## Learn Each Concept

### Start Here

New to Universal? Start with these pages in order:

1. [Custodians](custodians.md) - Understand where assets are held
2. [Merchants](merchants.md) - Learn who facilitates minting/burning
3. [Issuance](issuance.md) - See how uAssets are created
4. [Redemption](redemption.md) - Understand how uAssets are redeemed
5. [Proof of Reserves](reserves.md) - Verify the system trustlessly

### For Developers

Already understand the basics? Jump to:

- [API Documentation](../api.md)
- [SDK Reference](../sdk.md)
- [Smart Contracts](../smart-contracts.md)

## Questions?

- [FAQ](../../resources/support.md)
- [Discord](http://discord.gg/universalassets)
- [Contact team](../../introduction/core-contributors.md)
