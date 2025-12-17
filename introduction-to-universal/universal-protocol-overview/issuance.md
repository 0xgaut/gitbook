---
description: >-
  Token issuance refers to the process of increasing the supply of Universal
  tokens in circulation through minting.
---

# Issuance

<div align="center" data-full-width="false"><figure><img src="../../.gitbook/assets/Universal Token Issuance Flow" alt=""><figcaption><p>Universal Token Issuance Flow</p></figcaption></figure></div>

The issuance process is **managed by the Universal network** but can only be **initiated by a Merchant**. Each token issued is **fully collateralized** by its respective underlying asset. Additionally, Universal tokens can only be minted to **whitelisted destination addresses** of authorized Merchants.

#### **Sequence of Events for uAsset Issuance:**

1. **Merchant Initiation:**\
   The Merchant initiates the issuance process by sending a transaction to the Universal token contract. This transaction **signals intent** to issue Universal tokens, backed by an equivalent amount of the underlying asset.
2. **Asset Transfer to Custodian:**
   * The Merchant specifies the **blockchain and recipient address** where they wish to receive the minted Universal tokens.
   * The Merchant **transfers the corresponding underlying assets** to the designated Custodian.
   * The deposited assets must match the exact value of the Universal tokens being requested for issuance.
3. **Verification and Minting:**
   * The Universal network **validates the received assets** to ensure they match the requested issuance amount.
   * Once successfully verified, the network **executes a minting transaction**, creating the specified number of Universal tokens.
   * The newly minted tokens are then sent to the **Merchant’s designated address** on their specified blockchain.
