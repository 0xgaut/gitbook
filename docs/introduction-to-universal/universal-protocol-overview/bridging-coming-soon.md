---
description: >-
  Token bridging involves the process of reissuing a Universal token from one
  chain onto another.
hidden: true
---

# Bridging (coming soon)

<div data-full-width="false"><figure><img src="../../.gitbook/assets/Frame 12 (1).svg" alt=""><figcaption><p>Universal Token Bridging</p></figcaption></figure></div>

**Universal Token Bridging (coming soon)**

Token bridging enables the seamless transfer of **Universal tokens** from one blockchain to another without requiring the withdrawal or re-deposit of collateral with the custodian. This process ensures efficient cross-chain liquidity while maintaining the total token supply.

#### **Bridging Process:**

1. **Initiating the Bridge:**
   * The **Merchant** calls the **`bridge`** function in the smart contract, specifying the number of tokens to be transferred.
   * The specified token amount is **burned** on the source chain, reducing the Merchant’s Universal token balance.
2. **Finalization & Minting:**
   * After achieving **network finality** on the source chain, the equivalent number of Universal tokens is **minted** on the destination chain.
   * The newly minted tokens are **transferred** to the Merchant’s designated wallet address.
3. **Maintaining Token Supply Integrity:**
   * Since the tokens are **burned on the source chain** and **minted on the destination chain**, the **total supply of Universal tokens remains unchanged**.
   * This ensures that liquidity remains efficient and synchronized across all supported blockchains.
