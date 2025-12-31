# Protocol Overview

Universal is a wrapped asset protocol that enables seamless cross-chain asset trading through a secure, transparent infrastructure.

## Architecture

![Universal System Overview](../.gitbook/assets/Frame%202200.svg)

Universal operates through a network of permissioned entities and smart contracts that facilitate the minting, transfer, and redemption of uAssets across multiple blockchains.

## Core Components

### 1. uAssets (Universal Assets)

uAssets are ERC-20 or SPL tokens that represent 1:1-backed versions of underlying crypto assets. Examples include uBTC, uSOL, uDOGE, and 80+ others.

**Key properties:**
- Fully collateralized 1:1 with underlying assets
- Composable across DeFi protocols
- Native to each supported blockchain
- Instantly mintable and redeemable

### 2. Merchants

Merchants are permissioned entities authorized to mint and burn uAssets. They:

- Deposit collateral with Custodians
- Mint uAssets when users buy
- Burn uAssets when users sell
- Provide just-in-time liquidity
- Execute cross-chain conversions

[Learn more about Merchants →](protocol-concepts/merchants.md)

### 3. Custodians

Custodians securely hold the underlying assets that back uAssets. Currently, Universal uses Coinbase Prime for custody.

**Responsibilities:**
- Secure asset storage
- Collateral management for minting/burning
- Regulatory compliance
- Transparent reserve reporting

[Learn more about Custodians →](protocol-concepts/custodians.md)

### 4. Users

Users interact with Universal to:
- Trade uAssets on their preferred chain
- Mint uAssets from native assets (with KYC)
- Redeem uAssets for native assets (with KYC)
- Use uAssets in DeFi protocols

## How It Works

### Issuance Flow

When a user wants to acquire uAssets:

1. User requests a quote through Universal API
2. Merchant provides pricing based on market conditions
3. User signs the order
4. Merchant deposits underlying asset with Custodian (if needed)
5. Universal contracts mint uAssets to user's address
6. Transaction settles onchain

[Detailed issuance process →](protocol-concepts/issuance.md)

### Redemption Flow

When a user wants to convert uAssets back:

1. User initiates redemption request
2. Merchant provides quote for burning
3. User signs burn transaction
4. uAssets are burned from circulation
5. Custodian releases underlying assets to Merchant
6. User receives USDC or native asset

[Detailed redemption process →](protocol-concepts/redemption.md)

### Cross-Chain Transfers

Universal uses a burn-and-mint model for cross-chain transfers:

1. uAssets are burned on source chain
2. Equivalent uAssets are minted on destination chain
3. No liquidity fragmentation between chains
4. Single reserve pool backs all instances

## Just-in-Time Liquidity

Unlike traditional AMMs that require pre-deposited liquidity:

**Traditional DEX:**
- Liquidity providers deposit tokens in pools
- Capital sits idle until trades occur
- Impermanent loss risk for LPs
- Limited to available pool depth

**Universal JIT:**
- Merchants provide liquidity on-demand
- Capital deployed only when needed
- Optimized capital efficiency
- Access to offchain orderbook depth

## Security Model

### Reserve Backing

Every uAsset is backed 1:1 by reserves held with Coinbase Prime. Verify reserves anytime at [universal.xyz/reserves](https://www.universal.xyz/reserves).

### Proof of Reserves

Universal uses zkProofs (via Reclaim Protocol) to enable trustless verification:

- Cryptographic proof of Coinbase custody balances
- No exposure of sensitive API keys
- Real-time verification available
- Tamper-proof attestations

[Learn more about Proof of Reserves →](protocol-concepts/reserves.md)

### Smart Contract Audits

Universal smart contracts have been audited by leading security firms:

- [EVM Contracts Audit](https://github.com/r0bert-ethack/audits/blob/main/Alongside%20-%20Universal%20Contracts%20report%20-%20Final.pdf)
- [Solana Contracts Audit](https://hacken.io/audits/universal/)

### Permissioned Actors

Merchants and Custodians are permissioned entities that undergo vetting. This ensures:
- Regulatory compliance
- Operational reliability
- Security standards adherence
- Accountable counterparties

## Supported Chains

Universal currently operates on:

- **Base** - Ethereum L2
- **Arbitrum** - Ethereum L2
- **Polygon** - Ethereum sidechain
- **Solana** - High-performance L1
- **World** - World Chain
- **Katana** - Coming soon

Additional chains will be added based on demand and technical feasibility.

## Comparison with Other Protocols

| Feature | Universal | Bridges (LayerZero) | wBTC | CEX |
|---------|-----------|---------------------|------|-----|
| **Cross-Chain Liquidity** | Unified | Fragmented | Fragmented | Centralized |
| **Non-Smart Contract Assets** | Yes | No | Limited | Yes |
| **Capital Efficiency** | High (JIT) | Low (Locked) | Low (Pools) | High |
| **Composability** | Full | Full | Full | None |
| **Custody** | Regulated | Varies | Centralized | Centralized |
| **Verification** | zkProofs | Onchain | Attestations | None |

## Technical Specifications

### Token Standards

- **EVM chains**: ERC-20 compatible
- **Solana**: SPL Token standard

### Key Contracts

For contract addresses, see [Smart Contracts](smart-contracts.md).

### Oracles & Price Feeds

Universal uses multiple price sources for accurate quoting:
- Centralized exchange feeds
- Decentralized exchange prices
- Aggregated market data

## Next Steps

### For Developers

- [Integrate the API](api.md) for just-in-time liquidity
- [Use the SDK](sdk.md) for rapid development
- [Explore smart contracts](smart-contracts.md) for direct integration

### Learn Protocol Concepts

- [Merchants](protocol-concepts/merchants.md)
- [Custodians](protocol-concepts/custodians.md)
- [Issuance](protocol-concepts/issuance.md)
- [Redemption](protocol-concepts/redemption.md)
- [Proof of Reserves](protocol-concepts/reserves.md)

### Get Support

- [Discord](http://discord.gg/universalassets)
- [Contact Core Team](../introduction/core-contributors.md)
- Email: dev@universal.xyz
