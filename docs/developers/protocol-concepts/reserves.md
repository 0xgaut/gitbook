# Proof of Reserves

Universal's Proof of Reserves uses zkProofs and Coinbase API to enable trustless verification of asset holdings, ensuring every uAsset is 1:1 backed without exposing sensitive data.

## Introduction

Ensuring transparency and security is a fundamental principle of the Universal Protocol. To achieve **trustless verification** of our reserves, Universal integrates **Zero-Knowledge Proofs (zkProofs)** with **Coinbase Custody APIs**, allowing anyone to independently verify reserve holdings.

This approach guarantees that every **uAsset** is fully backed by its underlying collateral, providing users with confidence in the protocol's solvency while maintaining privacy and security.

Universal leverages [Reclaim Protocol's zkfetch](https://docs.reclaimprotocol.org/zkfetch) to power Proof of Reserves.

**Access Universal's Proof of Reserves:** [universal.xyz/reserves](https://www.universal.xyz/reserves)

## How Proof of Reserves Works

The Universal Protocol utilizes a **zkProof-powered verification system** that enables users to confirm our reserves without requiring trust in a third party. Below is a step-by-step breakdown of how it works:

### 1. Fetching Reserve Data from Coinbase API

- Universal calls the **Coinbase Custody API** to retrieve:
  - **Total balance** of each underlying asset (e.g., BTC, ETH, SOL)
  - **Organization information** (such as name and details)
- This data is essential to ensure that **all uAssets are 1:1 backed** by their respective reserves

### 2. Generating a Zero-Knowledge Proof

- Instead of directly revealing API responses, Universal leverages the Reclaim **zkProof system** to **cryptographically verify** the authenticity of the retrieved data
- A zkProof is generated for:
  - **Balances** – Ensuring that the reserve balances match the circulating supply of uAssets
  - **Organization Data** – Validating that the data originates from **Coinbase's API** without exposing sensitive information

### 3. User Verification & Trustless Transparency

- Users can independently verify the proof by querying Universal's zkProof verification system
- The verification process allows users to:
  - **Confirm that our reported reserves match on-chain balances**
  - **Ensure that Coinbase Custody holds the underlying collateral for uAssets**
  - **Authenticate that no sensitive data (e.g., API keys) has been exposed**

## Why Use zkProofs for Proof of Reserves?

Traditional Proof of Reserves (PoR) methods require users to trust third-party audits or manual attestations. By integrating **zkProofs**, Universal eliminates this trust assumption and enhances security.

### Key Benefits

**Privacy-Preserving**

Verifies the data **without revealing** private API keys or sensitive account details.

**Tamper-Proof**

Uses cryptographic proofs to ensure that **data cannot be altered**.

**Trustless Verification**

Users do not need to trust Universal; they can **independently verify reserves**.

**Automated & Continuous**

zkProofs allow **real-time reserve verification**, reducing reliance on periodic audits.

## How to Verify Universal's Proof of Reserves

Users can independently verify Universal's reserves using our **Proof of Reserves Dashboard**. The process is simple and requires no special access permissions:

### Verification Steps

1. Visit the [**Universal Proof of Reserves Dashboard**](https://www.universal.xyz/reserves)
2. Input the **desired asset (e.g., BTC, USDC, ETH)** to retrieve its proof
3. The system will return:
   - The **latest balance proof** generated from Coinbase API data
   - A **cryptographic zkProof** confirming the data's authenticity
4. Users can run the proof through a **verifier smart contract** or a local verification script to confirm its validity

### What You're Verifying

When you verify reserves, you confirm:

- **Reserve balance** matches or exceeds circulating uAsset supply
- **Data authenticity** - proof originated from Coinbase Custody API
- **Timestamp** - when the proof was generated
- **Integrity** - data hasn't been tampered with

## Technical Architecture

### Components

```
Coinbase API → zkProof Generation → Public Verification
     ↓                ↓                      ↓
  Balance Data    Cryptographic         User Verifies
                    Proof                 Trustlessly
```

### zkProof Properties

- **Soundness**: Impossible to create false proofs
- **Completeness**: Valid proofs always verify correctly
- **Zero-knowledge**: No sensitive data revealed

### Reclaim Protocol Integration

Universal uses [Reclaim Protocol's zkfetch](https://docs.reclaimprotocol.org/zkfetch) which provides:

- **HTTPS verification**: Proves data came from authentic API
- **Selective disclosure**: Reveal only necessary data points
- **Cryptographic attestation**: Unforgeable proof of API response

## Reserve Backing Formula

For each uAsset, Universal maintains:

```
Custodian Balance ≥ Circulating Supply
```

**Example:**
- Circulating uBTC: 100.5 BTC
- Coinbase Custody Balance: 100.5+ BTC
- Status: ✅ Fully backed

### Real-Time Monitoring

- Reserves updated continuously
- Minting/burning automatically adjusts supply
- Verification available 24/7

## Comparison with Traditional PoR

| Feature | Universal zkProof PoR | Traditional Audit |
|---------|----------------------|-------------------|
| **Verification** | Real-time, continuous | Periodic snapshots |
| **Trust Required** | None (cryptographic) | Auditor credibility |
| **Access** | Public, anyone can verify | Report publication |
| **Cost** | Automated, low cost | Expensive audits |
| **Transparency** | Full mathematical proof | Summary reports |

## For Developers

### Integrating Verification

Developers can integrate reserve verification into applications:

```typescript
// Conceptual example - check actual API docs
async function verifyReserves(asset: string) {
  const proof = await fetch(`https://www.universal.xyz/api/reserves/${asset}`);
  const isValid = await verifyZkProof(proof);
  return isValid;
}
```

### On-Chain Supply

Compare custodian reserves with on-chain circulating supply:

```typescript
// Check circulating supply
const totalSupply = await uBTCContract.totalSupply();

// Verify against reserves
const reserves = await getReserveBalance("BTC");
const isFullyBacked = reserves >= totalSupply;
```

## Security Considerations

### What zkProofs Protect Against

✅ **False balance claims**: Can't fake Coinbase API responses  
✅ **Data tampering**: Cryptographic integrity guarantees  
✅ **Outdated proofs**: Timestamps prevent replay attacks  

### What Users Should Still Consider

⚠️ **Custodian risk**: Coinbase custody reliability  
⚠️ **Oracle timing**: Small delays between proof and reality  
⚠️ **Smart contract risk**: Verify supply on-chain independently  

[Read full risk disclosures →](../../trade/risks.md)

## Transparency & Audits

### Open Source

- zkProof verification code is open source
- Smart contracts are audited and public
- Proof generation process documented

### Regular Audits

In addition to continuous zkProof verification:

- Smart contract audits ([EVM](https://github.com/r0bert-ethack/audits/blob/main/Alongside%20-%20Universal%20Contracts%20report%20-%20Final.pdf), [Solana](https://hacken.io/audits/universal/))
- Periodic third-party attestations
- Community verification encouraged

## Related Concepts

- [Custodians](custodians.md) - Where reserves are held
- [Issuance](issuance.md) - How reserves increase
- [Redemption](redemption.md) - How reserves decrease

## Resources

- [Verify Reserves Now](https://www.universal.xyz/reserves)
- [Reclaim Protocol Docs](https://docs.reclaimprotocol.org/zkfetch)
- [FAQ](../../resources/support.md)

## Questions?

- [Discord](http://discord.gg/universalassets)
- [Contact team](../../introduction/core-contributors.md)
