---
description: >-
  Custodians are responsible for the secure storage of the underlying assets
  that back Universal tokens. They play a crucial role in ensuring the security
  and integrity of the protocol’s reserves.
---

# Custodian

<figure><img src="../../../.gitbook/assets/Frame 2186.svg" alt=""><figcaption><p>Universal Custodian Structure</p></figcaption></figure>

**Responsibilities of a Custodian:**

* Asset Security: Stores the private keys that control the underlying crypto assets.
* Collateral Management: Manages deposits and withdrawals of assets used for the minting and burning of Universal tokens.
* Regulatory Compliance: Works with auditors and third parties to ensure transparent asset backing.

**Custodian Workflow:**

1. Merchants deposit collateral into dedicated Custodian-controlled wallets.
2. Custodian verifies and holds the assets, ensuring that every minted uAsset is fully backed.

Tokens are burned upon redemption, releasing the equivalent amount of collateral back to the Merchant.

{% hint style="info" %}
The current Custodian for uAssets is Coinbase Prime.
{% endhint %}
