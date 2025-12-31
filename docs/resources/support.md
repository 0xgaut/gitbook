# Support & FAQ

Find answers to commonly asked questions about the Universal Protocol and get help with any issues.

## Frequently Asked Questions

### General Questions

#### What is Universal Protocol?

Universal Protocol is a wrapped asset protocol that enables trading of any token, on any chain. By using an intent-based mint-and-burn mechanism, Universal eliminates liquidity fragmentation and improves cross-chain asset accessibility.

#### Who should use Universal Protocol?

Universal is designed for:

- **Onchain traders and investors** who want exposure to their favorite tokens on their preferred chain. For example, if you hold assets on Base but want exposure to Solana, you can swap for uSOL within Base without using a cross-chain bridge or centralized exchange.
- **Developers and dApp builders** who want to support a wider array of assets. A DEX on Base, for example, can enable trading of SOL, DOGE, and other assets that were previously unavailable.

#### Why is Universal needed?

Many cryptoassets (like BTC, DOGE, and XRP) lack smart contract functionality, preventing them from being available in DeFi spot markets. Universal solves this by:

- Bringing non-smart contract assets onchain so they can be traded like ERC-20 or SPL tokens
- Enhancing liquidity conditions by leveraging offchain order book depth, improving price execution
- Eliminating dependency on bridges and centralized exchanges to access cross-chain liquidity

#### Is Universal a bridge like LayerZero?

No, Universal is different from traditional bridges:

- **Bridges** (like LayerZero) use lock-and-mint. Assets are locked on Chain 1 to issue a representation on Chain 2, leading to fragmented liquidity
- **Universal** is closer to a Circle model: assets are held in reserves, and 1:1 wrapped versions are natively minted on destination chains
- Universal supports non-smart contract assets, whereas traditional bridges require smart contract compatibility

#### What is a wrapped asset?

A wrapped asset is an onchain token that represents an offchain or cross-chain asset.

- A custodian holds the underlying asset and issues a 1:1-backed token that can be minted or redeemed
- Universal's wrapped assets (uAssets) are maintained with Coinbase Prime and secured by the Universal Protocol's open-source smart contracts

#### Can I mint or redeem uAssets with or for the underlying native asset?

Yes. Upon successful KYC/KYB, anyone can [mint or redeem](https://www.universal.xyz/mint-and-redeem) uAssets with/for the native asset.

#### How is Universal better than wrapped BTC (wBTC)?

Universal improves on models like wBTC by:

- **Expanding availability**: uBTC can be issued on more chains, while wBTC is limited to select chains
- **Optimizing liquidity**: uAssets dynamically mint based on demand, whereas wBTC issuers must maintain deep liquidity pools on each chain
- **Enabling broader use cases**: Universal allows token issuers to expand their assets to non-EVM chains and DeFi ecosystems without deploying liquidity pools

#### Where are Universal's reserves stored?

All uAsset reserves are [verifiably](https://www.universal.xyz/reserves) held with Coinbase Prime, ensuring full transparency and security.

[Learn more about Proof of Reserves →](../developers/protocol-concepts/reserves.md)

#### Which chains does Universal support?

Currently, Universal supports Base, Polygon, Arbitrum, Solana, World, and soon Katana. The protocol will expand to additional chains based on demand.

#### What are Merchants?

Merchants are permissioned actors in the Universal ecosystem who can mint and burn uAssets. They help execute mint and redeems as well as cross-chain conversions.

[Learn more about Merchants →](../developers/protocol-concepts/merchants.md)

### Technical Questions

#### What is Just-in-Time (JIT) Liquidity?

DeFi typically relies on liquidity pools (LPs) to provide assets for swaps. However, LPs are capital inefficient because liquidity sits unused until needed.

Universal solves this with JIT Liquidity, meaning:

- Instead of pre-depositing liquidity, Merchants mint and fulfill orders dynamically when demand arises
- Liquidity is provided on an as-needed basis, optimizing capital efficiency

#### Are Universal Assets composable?

Yes, uAssets function like any ERC-20, SPL, or smart contract token, allowing them to integrate seamlessly into DeFi applications such as DEXs, lending markets, and derivatives.

#### How do Merchants fulfill quotes?

Merchants fulfill quotes using various exchange types, including:

- Intent-based exchanges like Uniswap X or CoWSwap, where orders are filled based on offchain intents
- Traditional liquidity sources where market makers dynamically provide liquidity

#### Do uAssets require secondary liquidity?

- For DEXs, UniV3 or CEXs, secondary liquidity is required but can be minimized due to dynamic minting
- For JIT fulfillment, liquidity is minted on-demand using offchain orderbooks, reducing the need for deep passive liquidity pools

#### How does Universal maintain transparency and security?

- uAssets are fully backed 1:1 by reserves held in regulated custodians
- All minting and redemption operations are transparent and verifiable onchain
- Merchants and custodians are permissioned entities, ensuring responsible management of assets
- Reserves can be audited at anytime using [zkProof verification](https://www.universal.xyz/reserves)

#### How does Universal compare to other cross-chain protocols?

| Feature | Universal | Bridges (LayerZero, Wormhole) | wBTC / RenBTC |
|---------|-----------|-------------------------------|---------------|
| **Cross-Chain Liquidity** | Yes | No (Fragmented per chain) | No (Fragmented) |
| **Non-Smart Contract Asset Support** | Yes | No | No |
| **Decentralization** | Permissioned Custodians & Merchants | Varies | Centralized Custodian |
| **Capital Efficiency** | High | Low (Requires locked liquidity) | Low (Requires deep liquidity pools) |

#### Can anyone become a Merchant?

No, Merchants are permissioned entities who must meet eligibility criteria. To apply, contact the Universal [Partnerships](../introduction/core-contributors.md) team.

**Can I request a large mint/redeem as an institution?**

Yes. Please contact us: austin@universal.xyz

#### How do I integrate Universal into my DeFi protocol?

Developers can integrate Universal in two ways:

1. **Via the Universal Relayer API** – A simple integration to enable seamless cross-chain asset trading
2. **Through smart contract interactions** – Developers can integrate Universal's contracts directly for advanced use cases

[View integration guides →](../developers/)

## Trading Support

### How do I place a trade?

Visit [app.universal.xyz](https://app.universal.xyz) and connect your wallet. Select the asset you want to trade, review the quote, and sign the transaction.

[Detailed trading guide →](../trade/placing-a-trade.md)

### What are the fees?

Fees vary based on:
- Asset being traded
- Trade size
- Network gas fees
- Referral fees (if applicable)

All fees are shown clearly in the quote before you confirm the trade.

### My transaction failed. What should I do?

Common reasons for failed transactions:

- **Insufficient gas**: Increase gas limit
- **Price movement**: Quote expired due to volatility - request a new quote
- **Insufficient balance**: Ensure you have enough tokens and gas
- **Approval needed**: Approve the Permit2 contract first

### How long does a trade take?

Trades typically settle within seconds once the transaction is confirmed onchain. Confirmation time depends on the blockchain:

- **Base, Arbitrum, Polygon**: 1-30 seconds
- **Solana**: 1-5 seconds

### Can I cancel a trade?

Once a signed order is submitted and confirmed onchain, it cannot be cancelled. Always review quotes carefully before signing.

## Developer Support

### Where can I find API documentation?

See the [API Documentation](../developers/api.md) for complete reference.

### How do I get an API key?

Contact dev@universal.xyz with your use case and organization details.

### Where are the smart contract addresses?

See [Smart Contracts](../developers/smart-contracts.md) for all contract addresses.

### Is there a TypeScript SDK?

Yes! Install with `npm install universal-sdk`. See the [SDK Documentation](../developers/sdk.md).

### Where are the audits?

- [EVM Contracts Audit](https://github.com/r0bert-ethack/audits/blob/main/Alongside%20-%20Universal%20Contracts%20report%20-%20Final.pdf)
- [Solana Contracts Audit](https://hacken.io/audits/universal/)

## Contact Support

### For Users

- **Discord**: [discord.gg/universalassets](http://discord.gg/universalassets)
- **Email**: support@universal.xyz

### For Developers

- **Discord Developer Chat**: [discord.gg/universalassets](http://discord.gg/universalassets)
- **Email**: dev@universal.xyz

### For Partnerships

- **Contact**: [Core Contributors](../introduction/core-contributors.md)
- **Email**: austin@universal.xyz

## Additional Resources

- [Introduction to Universal](../introduction/what-is-universal.md)
- [How Trading Works](../trade/how-it-works.md)
- [Protocol Overview](../developers/protocol.md)
- [Trading Risks](../trade/risks.md)

## Reporting Issues

### Security Issues

If you discover a security vulnerability, please email:

📩 **security@universal.xyz**

Do not post security issues publicly.

### Bug Reports

For non-security bugs:

- **Discord**: Report in the support channel
- **Email**: dev@universal.xyz

Please include:
- Description of the issue
- Steps to reproduce
- Expected vs actual behavior
- Screenshots if applicable
- Blockchain and transaction hash (if relevant)

## Community

Join the Universal community:

- **Discord**: [discord.gg/universalassets](http://discord.gg/universalassets)
- **Twitter/X**: [@UniversalAsset_](https://twitter.com/UniversalAsset_)
- **Website**: [universal.xyz](https://www.universal.xyz)

