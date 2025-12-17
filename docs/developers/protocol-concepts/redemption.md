# Redemption

Token redemption involves reducing the supply of Universal tokens by redeeming them for the underlying assets. This process can only be initiated by merchant addresses.

## Overview

![Universal Token Redemption Flow](../../.gitbook/assets/Universal%20Token%20Redemption%20Flow)

To redeem Universal tokens, the **Merchant** must call the **`burn`** function in the smart contract, specifying the exact amount of tokens to be burned.

## Redemption Process

### Sequence of Events for uAsset Redemption

#### 1. Burn Execution

When the **`burn`** function is executed:

- The specified token amount is **deducted** from the Merchant's on-chain Universal token balance
- The **total supply** of Universal tokens is **reduced accordingly**

#### 2. Collateral Release

Upon successful burning:

- The Merchant **exchanges the burned Universal tokens** for an equivalent amount of the **underlying asset**
- The **total circulating supply of Universal tokens decreases**, ensuring a **fully collateralized and transparent redemption process**
- Custodian releases underlying assets to Merchant
- User receives USDC or native assets (depending on redemption type)

## Redemption Types

### 1. Trading Redemption (Standard)

When users sell uAssets through the Universal app or API:

**Process:**
1. User requests quote to sell uAssets
2. Merchant provides sell price
3. User signs the transaction
4. Merchant burns uAssets
5. User receives USDC

**Settlement:**
- Instant settlement onchain
- USDC transferred to user wallet
- uAssets burned from circulation

### 2. Direct Redemption (Native Assets)

Qualified users can redeem uAssets for native assets:

**Requirements:**
- KYC/KYB verification
- Minimum redemption amounts may apply
- Settlement time depends on asset type

**Process:**
1. User initiates redemption request
2. Compliance verification
3. uAssets burned
4. Native asset transferred from custody
5. User receives native asset (e.g., actual BTC for uBTC)

📩 **Contact for direct redemption:** austin@universal.xyz

## Redemption Limits

### Per-Transaction Limits

- **Small redemptions**: Instant processing
- **Large redemptions**: May require additional verification
- **Merchant limits**: Based on available liquidity and risk management

### Chain Finality

Redemptions respect blockchain finality requirements:

- **EVM chains**: Wait for sufficient confirmations
- **Solana**: Wait for finalized status
- **Security**: Prevents double-spend attacks

## Cross-Chain Redemption

Users can redeem uAssets on any supported chain:

### Process

1. User holds uBTC on Base
2. Requests redemption on Base
3. uBTC burned on Base
4. User receives USDC on Base

**No bridge needed** - each chain operates independently with unified reserve backing.

## For Users

### Selling uAssets

When you sell uAssets through the Universal interface:

1. Request a sell quote
2. Review pricing and fees
3. Sign the transaction
4. Merchant burns your uAssets
5. Receive USDC in your wallet

The redemption mechanics are handled automatically—you don't interact directly with burn functions.

### Verification

After redemption, verify:

- **Transaction receipt**: Confirm burn on block explorer
- **Balance update**: Check USDC received
- **Supply change**: Observe total supply decreased (public data)

## Technical Implementation

### Smart Contract Burning

Simplified example of the burn function:

```solidity
// Conceptual example - not actual contract code
contract UniversalToken {
    mapping(address => bool) public authorizedMerchants;
    
    function burn(uint256 amount) external {
        require(authorizedMerchants[msg.sender], "Not authorized");
        require(balanceOf(msg.sender) >= amount, "Insufficient balance");
        
        _burn(msg.sender, amount);
        
        emit Burned(msg.sender, amount);
        
        // Signal to custodian to release collateral
        custodian.releaseCollateral(msg.sender, amount);
    }
}
```

### Key Security Features

- **Merchant authorization**: Only authorized Merchants can burn
- **Balance verification**: Sufficient balance must exist
- **Atomic operations**: Burn and collateral release are coordinated
- **Event emissions**: All burns are publicly auditable

## Redemption vs. Traditional Bridges

| Feature | Universal Redemption | Bridge Unlock |
|---------|---------------------|---------------|
| **Collateral** | Released from unified custody | Unlocked from bridge contract |
| **Settlement** | Instant (USDC) or T+1 (native) | Depends on bridge finality |
| **Liquidity** | Always available (Merchant backed) | Depends on locked liquidity |
| **Security** | Regulated custody + smart contract | Smart contract only |

## Monitoring Redemptions

### On-Chain Tracking

Track burn events on block explorers:

- View burn transactions
- Monitor total supply reductions
- Verify merchant addresses

### Reserve Verification

Confirm that redemptions reduce reserves proportionally:

1. Check supply decreased onchain
2. Verify reserves decreased at [universal.xyz/reserves](https://www.universal.xyz/reserves)
3. Confirm 1:1 backing maintained

[Learn more about reserve verification →](reserves.md)

## Redemption Fees

- **User trades**: Spread and fees included in sell quote
- **Direct redemption**: May include redemption fees for native asset withdrawal
- **Gas costs**: User pays blockchain gas fees

## Failed Redemptions

### Common Issues

**Insufficient Balance:**
- Ensure you have enough uAssets to redeem
- Account for gas fees

**Price Movement:**
- Quote expired due to price volatility
- Request a new quote

**Network Congestion:**
- Transaction may take longer during high traffic
- Increase gas price if needed

### Troubleshooting

1. Check transaction status on block explorer
2. Verify wallet balances
3. Contact support if issues persist

[View support resources →](../../resources/support.md)

## Related Concepts

- [Merchants](merchants.md) - Who can initiate redemptions
- [Custodians](custodians.md) - Where collateral is released from
- [Issuance](issuance.md) - The reverse process (minting)
- [Proof of Reserves](reserves.md) - Verifying backing changes

## Questions?

- [FAQ](../../resources/support.md)
- [Discord](http://discord.gg/universalassets)
- [Contact team](../../introduction/core-contributors.md)
