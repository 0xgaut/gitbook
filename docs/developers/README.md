# Developers

Build applications with Universal Protocol's wrapped asset infrastructure. Access 80+ cross-chain assets with deep liquidity and seamless execution.

## Integration Options

Universal provides three ways to integrate uAssets into your application:

<table>
  <tr>
    <td><strong><a href="api.md">Universal API</a></strong><br/>RESTful API for just-in-time minting and redemption via RFQ</td>
  </tr>
  <tr>
    <td><strong><a href="v4-hook.md">Uniswap V4 Hook</a></strong><br/>Atomic swaps via Uniswap V4 pools for supported assets</td>
  </tr>
  <tr>
    <td><strong>DEX Pool Integration</strong><br/>Route through existing DEX liquidity pools where available</td>
  </tr>
</table>

## Protocol Architecture

Understanding how Universal works:

<table>
  <tr>
    <td><strong><a href="protocol.md">Protocol Overview</a></strong><br/>High-level architecture and system design</td>
  </tr>
  <tr>
    <td><strong><a href="protocol-concepts/">Protocol Concepts</a></strong><br/>Deep dive into merchants, custodians, issuance, redemption, and reserves</td>
  </tr>
</table>

---

## Universal API

The **Universal API** allows developers to integrate uAssets with guaranteed pricing via Request-for-Quote (RFQ).

**Best for:**
- Wallets and trading interfaces
- DeFi aggregators
- Apps requiring guaranteed quotes

```typescript
import { UniversalRelayerSDK } from "universal-sdk";

const universal = new UniversalRelayerSDK();
const quote = await universal.getQuote({
  type: "BUY",
  token: "BTC",
  pair_token: "USDC",
  blockchain: "BASE",
  slippage_bips: 20,
  user_address: account.address,
  pair_token_amount: parseUnits("5", 6).toString(),
});
```

[API Documentation →](api.md) | [TypeScript SDK →](sdk.md)

---

## Uniswap V4 Hook

Leverage Universal's liquidity directly within Uniswap V4 pools for atomic, single-transaction swaps.

**Best for:**
- DeFi protocols needing atomic execution
- Composable onchain strategies
- Apps already supporting Uniswap V4

**Supported assets:** uTAO, uXRP, uZEC, uDOGE, uHYPE on Unichain (expanding)

[V4 Hook Documentation →](v4-hook.md)

---

## DEX Pool Integration

For assets with existing DEX liquidity, you can route swaps through standard AMM pools.

**Examples:** uSUI, uSOL, uXRP pools on Base

**Important considerations:**
- Universal does not manage DEX pool liquidity
- Secondary liquidity availability is not guaranteed
- Check pool depth before routing large trades
- Liquidity varies by asset and chain

This option works like any other DEX integration—query pool contracts directly or route via aggregators.

---

## Use Cases

### 1. Onchain Exchange with Deep Liquidity

Integrate uAssets into your DEX to offer trading pairs with deep liquidity and tighter spreads. The API provides just-in-time execution without requiring pre-funded liquidity pools.

### 2. Cross-Chain Lending Markets

Expand collateral options by integrating uAssets like uBTC, uSOL, uXRP, uDOGE into lending pools. Remove dependency on bridged assets with better execution and 1:1 collateralization.

### 3. Derivatives & Structured Products

Build options, futures, and structured products that support cross-chain assets. Access deep liquidity without fragmented order books.

### 4. Wallets with Native uAsset Support

Enable users to hold and trade 80+ cross-chain assets without requiring bridges. Provide seamless onboarding into DeFi by supporting instant swaps and conversions.

---

## Features

### 80+ Cross-Chain Assets

Access Bitcoin, Ethereum, Solana, Dogecoin, XRP, and 80+ other assets—all [fully backed 1:1](https://www.universal.xyz/reserves) with native assets.

### Just-in-Time Liquidity

Liquidity is provided dynamically at the moment of execution, eliminating the need for deep passive AMM liquidity pools.

### Offchain Orderbook Execution

The API allows users to tap into offchain order book liquidity through the merchant network while settling transactions fully onchain.

### Composable & DeFi-Ready

uAssets function like any ERC-20/SPL token, making them fully compatible with DEXs, lending platforms, structured products, and wallets.

---

## Resources

### Documentation

- [Protocol Overview](protocol.md)
- [API Reference](api.md)
- [SDK Documentation](sdk.md)
- [Contract Addresses](smart-contracts.md)
- [Asset Logos](asset-logos.md)

### Support

- [Discord Developer Chat](http://discord.gg/universalassets)
- [Partnership Inquiries](../introduction/core-contributors.md)
- API Access: dev@universal.xyz

### Audits

- [Universal EVM Audit](https://github.com/r0bert-ethack/audits/blob/main/Alongside%20-%20Universal%20Contracts%20report%20-%20Final.pdf)
- [Universal Solana Audit](https://hacken.io/audits/universal/)

---

## Monetization

Developers can add `referrer_address` and `referrer_bps` to quote requests to monetize their applications. Referrer fees are settled monthly.

[Learn about referrals →](../trade/referrals.md)

---

## Get in Touch

For partnership opportunities, custom integrations, or API access with higher rate limits:

- **Email:** austin@universal.xyz
- **Discord:** [discord.gg/universalassets](http://discord.gg/universalassets)

---

**Ready to build?** Start with the [Universal API](api.md) or explore [protocol concepts](protocol-concepts/).
