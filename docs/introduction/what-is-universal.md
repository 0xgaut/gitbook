# What is Universal?

Universal is a wrapped asset protocol designed to enable trading for any token, on any chain. Think Circle for crypto assets.

## The Universal Thesis

The crypto ecosystem faces three fundamental challenges:

1. **Limited Onchain Availability**: Many assets are not available onchain
2. **Liquidity Fragmentation**: Liquidity is split across multiple L1s and L2s, making it inefficient
3. **User Experience Complexity**: Users shouldn't have to worry about bridges, liquidity depth, or fragmented execution

By solving these issues, Universal makes all assets accessible across all supported chains.

## How Universal Works

Universal uses a **wrapped asset protocol** similar to wBTC, USDT, and USDC, but with key improvements:

### 1. Secure and Verifiable Custody

- All assets are [verifiably held](https://www.universal.xyz/reserves) by a qualified custodian (Coinbase Prime)
- This ensures 1:1 backing for all wrapped uAssets
- Zero-knowledge proofs enable trustless verification without exposing sensitive data

### 2. Instant Minting and Redemption

- uAssets can be minted and redeemed instantly on any chain once collateral is verified
- Merchants (permissioned actors) facilitate minting and redemption
- Just-in-time liquidity optimizes capital efficiency

### 3. Bringing Offchain Assets Onchain

- Universal enables non-EVM assets (like DOGE, XRP, BTC) to be wrapped and traded on any supported chain
- This expands the DeFi ecosystem by making previously unavailable assets accessible in onchain markets

## What are uAssets?

uAssets (e.g., uBTC, uSOL, uDOGE) are ERC-20/SPL tokens that represent 1:1-backed versions of underlying assets. These wrapped tokens solve liquidity problems builders face today.

### Key Characteristics

#### 1. Issued by Authorized Merchants

- uAssets are minted when a Merchant deposits the equivalent underlying collateral with a Custodian
- Issuance is instant for small orders but may require additional verification for large requests
- Issuance limits are determined by the Merchant's available offchain collateral

#### 2. Redeemable with Authorized Merchants

- Users can redeem uAssets via authorized Merchants who initiate a burn request
- Once burned, the underlying asset is released from custody, and the user receives USDC
- Redemptions follow chain finality requirements, ensuring security and preventing double-spending

#### 3. High Scalability & Liquidity Efficiency

- Unlike other solutions, uAssets are not constrained by compatibility or liquidity limitations
- Universal's burn-and-mint model optimizes capital efficiency, ensuring seamless movement across chains

#### 4. 1:1 Backing & Custodial Transparency

- Every uAsset is fully backed 1:1 by reserves held in regulated custody
- [Proof of reserves](https://www.universal.xyz/reserves) verification using zkPass enables users to verify holdings trustlessly

## Use Cases

### For Developers

#### 1. Onchain Exchange with Deep Liquidity

uAssets bring offchain order book depth onchain, enabling execution rivaling centralized exchanges (CEXs). A user on Base can trade uBTC/uSOL without relying on bridges.

#### 2. Enhanced Lending Markets

DeFi lending platforms can list uAssets to increase available collateral options. Previously illiquid assets like DOGE and XRP can now be utilized in lending protocols.

#### 3. Liquidity Improvement for Bridged Tokens

Create 1:1 pools between uAssets and existing bridged assets, reducing price impact on DEXs.

#### 4. Broader Index & Structured Products

Index tokens can now incorporate cross-chain assets, diversifying exposure across ecosystems. Options, futures, and structured DeFi products can leverage uAssets for better liquidity.

#### 5. Multi-Asset Wallet Support

Wallets can natively support uAssets, giving users a seamless experience across multiple chains. This removes the complexity of handling wrapped versions of assets per chain.

## Supported Chains

Universal currently supports:

- Base
- Polygon
- Arbitrum
- Solana
- World
- Katana (coming soon)

The protocol will expand to additional chains based on demand.

## Why Universal is Better

### vs. Traditional Bridges (LayerZero, Wormhole)

- **Universal**: Native minting on each chain with unified liquidity
- **Bridges**: Lock-and-mint creates fragmented liquidity per chain

### vs. wBTC / RenBTC

- **Universal**: Multi-chain by design, supports 80+ assets, dynamic minting
- **wBTC**: Limited chains, single asset, requires deep pre-deposited liquidity pools

### vs. Centralized Exchanges

- **Universal**: Fully onchain, composable with DeFi, self-custody
- **CEXs**: Offchain custody, not composable, withdrawal friction

## Next Steps

- Learn about the [core team](core-contributors.md) building Universal
- Explore the [protocol architecture](../developers/protocol.md) in detail
- Start [building with Universal](../developers/) in your application

## Resources

- [Whitepaper](https://app.universal.xyz/docs/universal-whitepaper.pdf)
- [Proof of Reserves](https://www.universal.xyz/reserves)
- [Universal EVM Audit](https://github.com/r0bert-ethack/audits/blob/main/Alongside%20-%20Universal%20Contracts%20report%20-%20Final.pdf)
- [Universal Solana Audit](https://hacken.io/audits/universal/)
