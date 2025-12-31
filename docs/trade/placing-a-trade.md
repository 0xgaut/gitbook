# Placing a Trade

This guide walks you through your first trade on Universal—from connecting your wallet to seeing tokens in your account.

## Before You Start

Make sure you have:

- **A Web3 wallet** — MetaMask, Coinbase Wallet, Rainbow, Phantom, or any WalletConnect-compatible wallet
- **Tokens to trade** — USDC, ETH, or any supported asset
- **Gas funds** — A small amount of the native token (ETH, MATIC, SOL, etc.) for transaction fees

## Step-by-Step Guide

### 1. Go to Universal

Visit [app.universalassets.xyz](https://app.universalassets.xyz) in your browser.

### 2. Connect Your Wallet

Click **Connect Wallet** in the top right. Select your wallet provider and approve the connection.

Universal supports:
- MetaMask
- Coinbase Wallet
- Rainbow
- Phantom (for Solana)
- WalletConnect (200+ wallets)

### 3. Select Your Network

Choose the blockchain you want to trade on. Your options include:
- Base
- Polygon
- Arbitrum
- Solana
- World

Make sure your wallet is connected to the same network.

### 4. Choose Your Trade

**Select what you're selling** (top field)
- Click the token selector
- Choose from your wallet balances or search for an asset
- Enter the amount you want to sell

**Select what you're buying** (bottom field)
- Click the token selector
- Browse 80+ available assets
- Look for the **"U" badge** on uAssets

The app shows your quote instantly, including the exact amount you'll receive.

### 5. Review the Quote

Before confirming, check:

| Detail | What It Means |
|--------|---------------|
| **You Pay** | Amount leaving your wallet |
| **You Receive** | Amount arriving in your wallet |
| **Rate** | Exchange rate for this trade |
| **Price Impact** | How your trade affects the price (usually minimal) |
| **Network Fee** | Gas cost for the transaction |

Quotes are valid for a limited time. If the price moves significantly, you'll get a fresh quote.

### 6. Approve Tokens (First Time Only)

If this is your first trade with a particular token on an EVM chain, you'll need to approve it:

1. Click **Approve**
2. Your wallet opens with an approval request
3. Confirm the transaction
4. Wait for the approval to process (usually a few seconds)

This uses **Permit2**, a secure approval standard. You only do this once per token—future trades skip this step.

### 7. Sign and Execute

1. Click **Swap** (or **Trade**)
2. Your wallet prompts you to sign
3. Review the details and confirm
4. The trade executes onchain

### 8. Done!

Your new tokens appear in your wallet within seconds. You can:
- View the transaction on the block explorer (click the transaction link)
- See your updated balances in the app
- Trade again or explore other assets

---

## Trading Tips

**Start with a small trade**
Get comfortable with the flow before trading larger amounts.

**Keep gas funds handy**
Always maintain a small balance of native tokens (ETH, MATIC, SOL) for transaction fees.

**Check the rate before confirming**
The quote shows exactly what you'll receive. If it looks off, refresh and try again.

**Use the right network**
Make sure your wallet is on the same chain you're trading on. The app will prompt you to switch if needed.

---

## Common Questions

### Why do I need to approve?

Token approvals let Universal's smart contracts move tokens on your behalf. Permit2 makes this safer and more efficient than traditional approvals.

### How long does a trade take?

Most trades settle in seconds. Exact timing depends on blockchain congestion.

### What if the price changes?

If the price moves beyond your slippage tolerance, the trade won't execute and you won't be charged (except for any gas used).

### Can I cancel a trade?

Once you sign and submit, the trade executes onchain and cannot be reversed. Always review before confirming.

---

## Troubleshooting

### "Transaction Failed"

- **Insufficient gas**: Add more native tokens to cover fees
- **Price moved**: The quote expired—try again with a fresh quote
- **Network congestion**: Wait a moment and retry

### "Insufficient Balance"

Make sure you have enough of the token you're selling, plus gas funds for the transaction.

### "Approval Stuck"

If an approval transaction is pending:
1. Wait—it may just be slow
2. Check the transaction on a block explorer
3. If stuck, you can speed it up or cancel through your wallet

### Wallet Won't Connect

- Refresh the page
- Make sure your wallet extension is unlocked
- Try a different browser or disable conflicting extensions
- Clear your browser cache

Still stuck? [Get help on Discord](http://discord.gg/universalassets)

---

## Next Steps

- [Earn rewards with referrals](referrals.md)
- [Learn about points](points.md)
- [Understand trading risks](risks.md)
