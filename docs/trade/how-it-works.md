# How Trading Works

Universal lets you trade any token on your preferred blockchain—even if that token doesn't exist there natively. Here's how it works.

## The Basics

When you trade on Universal, you're swapping between **uAssets**—wrapped versions of tokens like BTC, SOL, DOGE, and 80+ others. Each uAsset is backed 1:1 by the real asset held in regulated custody.

You'll recognize uAssets by the **"U" badge** on their icon in the app.

## What Happens When You Trade

### Step 1: Enter Your Trade

Choose the token you want to buy and the token you want to sell. Universal shows you a quote with the exact amount you'll receive.

### Step 2: Approve (First Time Only)

If this is your first trade on an EVM chain, you'll approve Universal to access your tokens. This is a one-time setup using Permit2—after that, future trades only require a signature.

### Step 3: Sign the Transaction

Review the details and sign. Your wallet will prompt you to confirm.

### Step 4: Trade Executes

Your trade settles onchain in seconds. The tokens appear in your wallet automatically.

That's it. No bridging, no complicated steps, no waiting.

---

## Where Does Liquidity Come From?

Universal connects you to **Merchants**—professional market makers who provide pricing and fulfill trades. When you buy a uAsset:

1. You send payment (like USDC)
2. The Merchant mints fresh uAssets backed by real collateral
3. You receive the uAssets in your wallet

When you sell, the reverse happens—your uAssets are burned, and you receive payment.

This **just-in-time liquidity** model means:
- No liquidity pools to drain
- Consistent pricing regardless of trade size
- Capital efficiency that translates to better rates

---

## Native vs. Non-Native Tokens

Universal intelligently routes your trade based on what you're swapping:

**Native tokens** (like ETH on Ethereum or SOL on Solana)
→ Routed through DEX aggregators for optimal pricing

**Non-native tokens** (like BTC on Base or DOGE on Polygon)
→ Handled by Universal's Merchant network

You don't need to think about this—the app handles routing automatically.

---

## Pricing & Fees

Universal provides transparent, competitive pricing:

- **Spread**: Small difference between buy and sell prices (how Merchants earn)
- **Protocol fee**: Minimal fee supporting the Universal network
- **Gas**: Standard blockchain transaction fees

All fees are included in the quote you see before trading.

---

<details>
<summary><strong>Dive Deeper: How Trades Execute Behind the Scenes</strong></summary>

### Two Execution Methods

Universal uses two systems to execute trades, chosen automatically based on context:

**Universal API (Request-for-Quote)**

Most trades use our RFQ system, similar to UniswapX:
1. You request a quote
2. Merchants compete to offer the best price
3. You sign an EIP-712 message (gasless signature)
4. The winning Merchant fulfills your order onchain

This approach guarantees the exact price you were quoted.

**Uniswap V4 Hook**

For apps that need atomic, single-transaction swaps:
1. Trade executes directly through a Uniswap V4 pool
2. Merchants provide just-in-time liquidity via the Hook
3. Settlement happens in one transaction

The V4 Hook requires an additional signature but enables instant composability with other DeFi protocols.

### Why Two Systems?

- **API**: Best for wallets and apps wanting guaranteed quotes and gasless UX
- **V4 Hook**: Best for DeFi integrations needing atomic execution

As a trader, you don't need to choose—the app selects the optimal path.

</details>

---

## What Makes This Different?

| Traditional DEX | Universal |
|----------------|-----------|
| Limited to native chain tokens | Trade 80+ tokens on any chain |
| Liquidity depends on pool depth | Consistent liquidity from Merchants |
| Price impact on large trades | Stable pricing at any size |
| Need bridges for cross-chain | No bridges required |

---

## Security & Backing

Every uAsset is 1:1 backed by real assets in regulated custody (Coinbase). You can verify reserves anytime at [universal.xyz/reserves](https://www.universal.xyz/reserves).

[Learn more about Proof of Reserves →](../developers/protocol-concepts/reserves.md)

---

## Next Steps

- [Place your first trade](placing-a-trade.md)
- [Understand the risks](risks.md)
- [Get support](../resources/support.md)
