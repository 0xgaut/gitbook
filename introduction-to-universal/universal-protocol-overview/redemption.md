---
description: >-
  Token redemption involves reducing the supply of Universal tokens by redeeming
  them for the underlying assets. This process can only be initiated by merchant
  addresses.
---

# Redemption

<figure><img src="../../.gitbook/assets/Universal Token Redemption Flow" alt=""><figcaption><p>Universal Token Redemption Flow</p></figcaption></figure>

To redeem Universal tokens, the **Merchant** must call the **`burn`** function in the smart contract, specifying the exact amount of tokens to be burned.

#### **Redemption Process:**

1. When the **`burn`** function is executed:
   * The specified token amount is **deducted** from the Merchant’s onchain Universal token balance.
   * The **total supply** of Universal tokens is **reduced accordingly**.
2. Upon successful burning:
   * The Merchant **exchanges the burned Universal tokens** for an equivalent amount of the **underlying asset**.
   * The **total circulating supply of Universal tokens decreases**, ensuring a **fully collateralized and transparent redemption process**.
