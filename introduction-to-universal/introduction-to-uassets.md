---
description: >-
  uAssets (e.g. uBTC, uSOL, ...) are ERC-20 tokens that can solve liquidity
  problems builders face today.
---

# Introduction to uAssets

## Introduction to uAssets

### What are uAssets?

uAssets are wrapped, cross-chain compatible assets (e.g., uBTC, uSOL) issued by the Universal Protocol to solve liquidity fragmentation across blockchains. These assets allow users to access, trade, and transfer previously unavailable tokens across multiple networks.

### Key Characteristics of uAssets

#### 1. Issuance by Authorized Merchants

* uAssets are minted when a Merchant deposits the equivalent underlying collateral with a Custodian.
* The issuance process is instant for small orders but may require additional verification for large requests.
* Issuance limits are determined by the Merchant’s available offchain collateral.

#### 2. Redeemable with Authorized Merchants

* Users can redeem uAssets via authorized Merchants who initiate a burn request.
* Once burned, the underlying asset is released from custody, and the user receives USDC.
* Redemptions follow chain finality requirements, ensuring security and preventing double-spending.

#### 3. High Scalability & Liquidity Efficiency

* Unlike other solutions, uAssets are not constrained by compatibility or liquidity limitations.
* Universal’s burn-and-mint model optimizes capital efficiency, ensuring seamless movement across chains.

#### 4. 1:1 Backing & Custodial Transparency

* Every uAsset is fully backed 1:1 by reserves held in a regulated custodian (e.g., Coinbase Custody).
* [Proof of reserves](https://www.universal.xyz/reserves) verification using zkPass enables users to verify holdings trustlessly.

***

### Use Cases of uAssets for Developers

#### 1. Onchain Exchange with Deep Liquidity

* uAssets bring offchain order book depth onchain, enabling execution rivaling centralized exchanges (CEXs).
* Example: A user on Base can trade uBTC/uSOL without relying on bridges.

#### 2. Enhanced Lending Markets

* DeFi lending platforms can list uAssets to increase available collateral options.
* Previously illiquid assets like DOGE and XRP can now be utilized in lending protocols.

#### 3. Liquidity Improvement for Bridged Tokens

* Creates 1:1 pools between uAssets and existing bridged assets, reducing price impact on DEXs.

#### 4. Broader Index & Structured Products

* Index tokens can now incorporate cross-chain assets, diversifying exposure across ecosystems.
* Options, futures, and structured DeFi products can leverage uAssets for better liquidity.

#### 5. Multi-Asset Wallet Support

* Wallets can natively support uAssets, giving users a seamless experience across multiple chains.
* Removes the complexity of handling wrapped versions of assets per chain.
