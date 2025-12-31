# Custodians

Custodians are responsible for the secure storage of the underlying assets that back Universal tokens. They play a crucial role in ensuring the security and integrity of the protocol's reserves.

## Overview

![Universal Custodian Structure](../../.gitbook/assets/Frame%202186.svg)

Custodians provide institutional-grade security for the crypto assets that back every uAsset in circulation. Currently, Universal uses **Coinbase Prime** as its primary custodian.

## Responsibilities

### Asset Security

Custodians store the private keys that control the underlying crypto assets:

- Cold storage for majority of assets
- Multi-signature requirements
- Hardware security modules (HSMs)
- Regular security audits

### Collateral Management

Custodians manage deposits and withdrawals of assets used for the minting and burning of Universal tokens:

- Verify Merchant collateral deposits
- Hold assets in segregated accounts
- Release assets upon burn confirmations
- Maintain accurate real-time balances

### Regulatory Compliance

Custodians work with auditors and third parties to ensure transparent asset backing:

- Regular attestations
- Third-party audits
- Regulatory reporting
- KYC/AML compliance

## Custodian Workflow

### 1. Collateral Deposit

When a Merchant wants to mint uAssets:

- Merchant transfers underlying assets to Custodian-controlled wallets
- Custodian verifies receipt and amount
- Collateral is allocated to Merchant's account
- Universal protocol is notified of available collateral

### 2. Collateral Holding

While uAssets circulate:

- Custodian securely holds 1:1 backing assets
- Assets remain in custody until redemption
- Regular proof of reserves verification
- Transparent reporting via zkProofs

### 3. Collateral Release

When uAssets are redeemed:

- Universal protocol initiates burn transaction
- Custodian verifies burn confirmation
- Underlying assets released to Merchant
- Tokens are permanently removed from circulation

## Current Custodian

### Coinbase Prime

Universal's reserves are held with [Coinbase Prime](https://prime.coinbase.com/), an institutional custody platform offering:

**Security Features:**
- 98%+ of assets in cold storage
- Multi-layer security infrastructure
- Insurance coverage
- SOC 2 Type II certified

**Regulatory Standing:**
- Licensed and regulated
- Regular audits and attestations
- Compliance with global standards

**Operational Excellence:**
- 24/7 monitoring
- Proven track record
- Deep liquidity access

## Verification

### Proof of Reserves

Anyone can verify that Custodian holdings match circulating uAssets:

1. Visit [universal.xyz/reserves](https://www.universal.xyz/reserves)
2. View real-time reserve balances
3. Verify zkProof attestations
4. Confirm 1:1 backing

[Learn more about Proof of Reserves →](reserves.md)

### Transparency

Universal maintains full transparency around custody:

- **Public verification**: zkProof system enables trustless verification
- **Onchain tracking**: All mints and burns are publicly visible
- **Regular reporting**: Reserve updates published regularly

## Multi-Custodian Future

While Universal currently uses Coinbase Prime, the protocol is designed to support multiple custodians:

**Benefits:**
- Reduced concentration risk
- Geographic diversification
- Competitive custody fees
- Increased resilience

**Requirements for additional custodians:**
- Institutional-grade security
- Regulatory compliance
- API integration capabilities
- Insurance coverage

## For Users

### What this means for you

Your uAssets are backed by assets held in institutional custody:

✅ **Secure**: Industry-leading custody infrastructure  
✅ **Transparent**: Verifiable reserves via zkProofs  
✅ **Regulated**: Compliant custody provider  
✅ **Insured**: Coverage for custodial assets  

### Risk Considerations

While custody with Coinbase Prime provides strong security, users should understand:

- **Custodial risk**: Assets held by third party
- **Counterparty risk**: Dependence on custodian operations
- **Regulatory risk**: Changes in custody regulations

[Read full risk disclosures →](../../trade/risks.md)

## Technical Integration

### API Communication

Custodians integrate with Universal via secure APIs:

```typescript
// Conceptual example - not actual API
interface CustodianAPI {
  // Verify collateral balance
  verifyBalance(merchant: string, asset: string): Promise<Balance>;
  
  // Confirm deposit
  confirmDeposit(txHash: string): Promise<Confirmation>;
  
  // Request withdrawal
  requestWithdrawal(merchant: string, asset: string, amount: bigint): Promise<void>;
}
```

### Data Feeds

Custodians provide:
- Real-time balance updates
- Transaction confirmations
- Attestation data for zkProofs

## Related Concepts

- [Merchants](merchants.md) - Who deposits collateral with Custodians
- [Issuance](issuance.md) - How Custodian holdings enable minting
- [Redemption](redemption.md) - How holdings are released on burns
- [Proof of Reserves](reserves.md) - How to verify Custodian balances

## Questions?

- [FAQ](../../resources/support.md)
- [Discord](http://discord.gg/universalassets)
- [Verify reserves](https://www.universal.xyz/reserves)
