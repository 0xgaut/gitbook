# Issuance

Token issuance refers to the process of increasing the supply of Universal tokens in circulation through minting.

## Overview

![Universal Token Issuance Flow](../../.gitbook/assets/Universal%20Token%20Issuance%20Flow)

The issuance process is **managed by the Universal network** but can only be **initiated by a Merchant**. Each token issued is **fully collateralized** by its respective underlying asset. Additionally, Universal tokens can only be minted to **whitelisted destination addresses** of authorized Merchants.

## Issuance Process

### Sequence of Events for uAsset Issuance

#### 1. Merchant Initiation

The Merchant initiates the issuance process by sending a transaction to the Universal token contract. This transaction **signals intent** to issue Universal tokens, backed by an equivalent amount of the underlying asset.

#### 2. Asset Transfer to Custodian

- The Merchant specifies the **blockchain and recipient address** where they wish to receive the minted Universal tokens
- The Merchant **transfers the corresponding underlying assets** to the designated Custodian
- The deposited assets must match the exact value of the Universal tokens being requested for issuance

#### 3. Verification and Minting

- The Universal network **validates the received assets** to ensure they match the requested issuance amount
- Once successfully verified, the network **executes a minting transaction**, creating the specified number of Universal tokens
- The newly minted tokens are then sent to the **Merchant's designated address** on their specified blockchain

## Issuance Limits

### Per-Transaction Limits

- **Small orders**: Instant minting for standard transaction sizes
- **Large orders**: May require additional verification time for risk management
- **Merchant limits**: Based on available collateral with Custodian

### Collateral Requirements

Every uAsset minted requires:

- **1:1 backing**: Equivalent underlying asset must be held in custody
- **Verified deposit**: Custodian must confirm receipt of collateral
- **Chain finality**: Deposits must reach finality before minting

## Cross-Chain Issuance

uAssets can be minted on any supported blockchain:

### Native Issuance

When minting on a new chain:

1. Merchant deposits collateral with Custodian
2. Custodian verifies and allocates collateral
3. Smart contract on target chain mints uAssets
4. No bridge required—native minting

### Benefits

- **No liquidity fragmentation**: Single collateral pool backs all chains
- **Instant availability**: Assets available on any chain immediately
- **Unified reserves**: Easier to verify and manage backing

## For Users

### Buying uAssets

When you buy uAssets through the Universal app or API:

1. You request a quote for buying uAssets
2. A Merchant provides pricing
3. You sign the order
4. Merchant handles collateral and minting behind the scenes
5. You receive uAssets in your wallet

The issuance process is abstracted—you don't need to worry about Merchants or Custodians.

### Minting Directly

Qualified users (institutions, high-net-worth individuals) can mint uAssets directly:

1. Complete KYC/KYB verification
2. Deposit native assets with Universal
3. Receive equivalent uAssets
4. Pay minting fees (if applicable)

📩 **Contact for direct minting:** austin@universal.xyz

## Technical Implementation

### Smart Contract Minting

Simplified example of the minting function:

```solidity
// Conceptual example - not actual contract code
contract UniversalToken {
    mapping(address => bool) public authorizedMerchants;
    
    function mint(address to, uint256 amount) external {
        require(authorizedMerchants[msg.sender], "Not authorized");
        require(custodian.verifyCollateral(msg.sender, amount), "Insufficient collateral");
        
        _mint(to, amount);
        
        emit Minted(to, amount, msg.sender);
    }
}
```

### Key Security Features

- **Merchant whitelist**: Only authorized Merchants can mint
- **Collateral verification**: Custodian must confirm backing
- **Atomic operations**: Minting and collateral lock happen atomically
- **Event emissions**: All mints are publicly auditable onchain

## Issuance vs. Traditional Bridges

| Feature | Universal Issuance | Bridge Lock-and-Mint |
|---------|-------------------|----------------------|
| **Collateral** | Unified custody pool | Locked per chain |
| **Liquidity** | Shared across chains | Fragmented per chain |
| **Speed** | Instant | Depends on finality |
| **Security** | Regulated custody | Smart contract risk |
| **Verification** | zkProof reserves | On-chain balance |

## Monitoring Issuance

### On-Chain Tracking

Track minting events on block explorers:

- View mint transactions
- Monitor total supply changes
- Verify merchant addresses

### Reserve Verification

Confirm that issuance matches reserve growth:

1. Check circulating supply onchain
2. Verify reserves at [universal.xyz/reserves](https://www.universal.xyz/reserves)
3. Confirm 1:1 backing maintained

[Learn more about reserve verification →](reserves.md)

## Issuance Fees

- **User trades**: Spread and fees included in quote
- **Direct minting**: Institutional minting may include fees
- **Gas costs**: User pays blockchain gas fees

## Related Concepts

- [Merchants](merchants.md) - Who can initiate issuance
- [Custodians](custodians.md) - Where collateral is held
- [Redemption](redemption.md) - The reverse process (burning)
- [Proof of Reserves](reserves.md) - Verifying backing

## Questions?

- [FAQ](../../resources/support.md)
- [Discord](http://discord.gg/universalassets)
- [Contact team](../../introduction/core-contributors.md)
