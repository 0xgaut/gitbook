---
description: >-
  Universal's Proof of Reserves uses zkProofs and Coinbase API to enable
  trustless verification of asset holdings, ensuring every uAsset is 1:1 backed
  without exposing sensitive data.
---

# Proof of Reserves

### **Introduction**

Ensuring transparency and security is a fundamental principle of the Universal Protocol. To achieve **trustless verification** of our reserves, Universal integrates **Zero-Knowledge Proofs (zkProofs)** with **Coinbase Custody APIs**, allowing anyone to independently verify reserve holdings.

This approach guarantees that every **uAsset** is fully backed by its underlying collateral, providing users with confidence in the protocol's solvency while maintaining privacy and security.\
\
Universal leverages [Reclaim Protocol's ](https://docs.reclaimprotocol.org/zkfetch)[zkfetch](https://docs.reclaimprotocol.org/zkfetch) to power Proof of Reserves.\
\
Access Universal's Proof of Reserves [here](https://www.universal.xyz/reserves).

***

### **How Proof of Reserves Works**

The Universal Protocol utilizes a **zkProof-powered verification system** that enables users to confirm our reserves without requiring trust in a third party. Below is a step-by-step breakdown of how it works:

#### **1. Fetching Reserve Data from Coinbase API**

* Universal calls the **Coinbase Custody API** to retrieve:
  * **Total balance** of each underlying asset (e.g., BTC, ETH..)
  * **Organization information** (such as name and details)
* This data is essential to ensure that **all uAssets are 1:1 backed** by their respective reserves.

#### **2. Generating a Zero-Knowledge Proof**

* Instead of directly revealing API responses, Universal leverages the Reclaim **zkProof system** to **cryptographically verify** the authenticity of the retrieved data.
* A zkProof is generated for:
  * **Balances** – Ensuring that the reserve balances match the circulating supply of uAssets.
  * **Organization Data** – Validating that the data originates from **Coinbase’s API** without exposing sensitive information.

#### **3. User Verification & Trustless Transparency**

* Users can independently verify the proof by querying Universal's zkProof verification system.
* The verification process allows users to:
  * **Confirm that our reported reserves match onchain balances.**
  * **Ensure that Coinbase Custody holds the underlying collateral for uAssets.**
  * **Authenticate that no sensitive data (e.g., API keys) has been exposed.**

***

### **Why Use zkProofs for Proof of Reserves?**

Traditional Proof of Reserves (PoR) methods require users to trust third-party audits or manual attestations. By integrating **zkProofs**, Universal eliminates this trust assumption and enhances security.



**Privacy-Preserving:** Verifies the data **without revealing** private API keys or sensitive account details.&#x20;

**Tamper-Proof:** Uses cryptographic proofs to ensure that **data cannot be altered**.&#x20;

**Trustless Verification:** Users do not need to trust Universal; they can **independently verify reserves**.&#x20;

**Automated & Continuous:** zkProofs allow **real-time reserve verification**, reducing reliance on periodic audits.

***

### **How to Verify Universal's Proof of Reserves**

Users can independently verify Universal's reserves using our **Proof of Reserves Dashboard**. The process is simple and requires no special access permissions:

1. Visit the [**Universal Proof of Reserves Dashboard**](https://www.universal.xyz/reserves)
2. Input the **desired asset (e.g., BTC, USDC, ETH)** to retrieve its proof.
3. The system will return:
   * The **latest balance proof** generated from Coinbase API data.
   * A **cryptographic zkProof** confirming the data’s authenticity.
4. Users can run the proof through a **verifier smart contract** or a local verification script to confirm its validity.
