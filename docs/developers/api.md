# Universal API

Integrate just-in-time mint/redeems of 80+ uAssets directly in your app or protocol.

## Overview

The **Universal API** allows developers to seamlessly integrate **uAssets** into their applications and optionally earn fees. The API acts as an intermediary, connecting users with **Merchants** to facilitate buying, selling, and cross-chain transactions.

### What is the Universal API?

DeFi typically relies on liquidity pools (LPs) to provide assets for swaps. However, LPs are capital inefficient because liquidity sits unused until needed.

Universal solves this with JIT (just-in-time) Liquidity via the **Universal API** meaning:

- Instead of pre-depositing liquidity, Merchants mint and fulfill orders dynamically when demand arises
- Liquidity is provided on an as-needed basis, optimizing capital efficiency

> **Note**: To obtain API access with higher rate limits, please reach out to the Universal [Partnerships](../introduction/core-contributors.md) team for an API key.

### Prerequisites

**EVM Chains:**

Users will need to approve the Permit2 contract address `0x000000000022D473030F116dDEE9F6B43aC78BA3` to spend USDC (or token address to be used for purchase) and the uAsset address for selling.

**Solana:**

Users will need to create a token account for the uAsset they're acquiring and approve the Universal Solana program id: `3UcHkqbtMGtRZmWNGGfC6wwi9f7uGBacTBbuRgrGyoLG`

### Monetization

Developers are able to add `referrer_address` and `referrer_bps` to the quote request to monetize their app. `referrer_bps` charge will be added on to every order, and the fees will be settled periodically (currently, at the end of every month) to `referrer_address`.

[Learn more about referrals →](../trade/referrals.md)

## Quick Start

For the fastest integration, use the [Universal SDK](sdk.md). For direct API access, continue reading below.

## API Basics

### Base URL

```
https://relayer.universal.xyz/api (with an API key)
https://www.universal.xyz/api/v1 (without an API key)
```

### Authentication

All API requests require authentication via a **Bearer Token**.

```
Authorization: Bearer <YOUR_API_KEY>
```

Ensure that your API key is included in every request to authenticate and authorize API usage.

## Type Definitions

### Token Names

`TokenName`: `BTC, SOL, XRP, DOGE, DOT, NEAR, LTC, ADA, BCH, ALGO, ...`

See [full asset list](smart-contracts.md) for all supported tokens.

### Pair Token

`PairTokenName`: `USDC`

### Blockchains

`BlockchainName`: `BASE | ARBITRUM | POLYGON | WORLD | SOLANA`

### Quote Type

```typescript
interface Quote {
  type: "BUY" | "SELL";
  token: TokenName;
  token_amount: string; // wei / lamport
  pair_token: PairTokenName | string; // USDC or token address 
  slippage_bips: number; // 0-100
  blockchain: BlockchainName;
  deadline: string; // unix epoch
  pair_token_amount: string; // wei / lamport
  id: string;
  user_address: string; 
  merchant_address: string; 
  gas_fee_nominal: string; // wei / lamport
  gas_fee_dollars: number; // e.g. $1.23 = 1.23
  relayer_nonce: number;
  merchant_id: string; 
  mode: "DIRECT" | "BRIDGED"
}
```

## API Endpoints

### 1. Request a Quote

To retrieve a **price quote** for a uAsset transaction, make a `POST` request to:

```
POST /quote
```

#### Request Body

```typescript
{
  type: "BUY" | "SELL";
  token: TokenName;
  token_amount?: string;
  pair_token_amount?: string;
  pair_token: PairTokenName;
  slippage_bips: number;
  blockchain: BlockchainName;
  user_address: string;
  referrer_address?: string;
  referrer_bps?: string;
}
```

#### Parameters

- **`type`**: Specifies if the user wants to `BUY` or `SELL`
- **`token`**: The uAsset being bought or sold
- **`token_amount`**: (Optional) The amount of the asset being transacted
- **`pair_token`**: The stable asset used for the trade (e.g., USDC)
- **`pair_token_amount`**: (Optional) The amount of the paired asset
- **`slippage_bips`**: Slippage tolerance in basis points (0-100)
- **`blockchain`**: The blockchain network where the transaction is executed
- **`user_address`**: The wallet address initiating the trade
- **`referrer_address`**: (Optional) The address to send referrer fees to
- **`referrer_bps`**: (Optional) Basis points to charge the user as a referrer

#### Response (200 OK)

Upon success, the API returns a quote containing:

- Quoted amounts
- Gas fees
- Merchant details
- A unique quote `ID` for later use

```typescript
{
  ...Quote
}
```

### 2. Sign the Quote

The user must sign the quote using **EIP-712 signature standards** (for EVM) or Solana transaction signing before submitting the order.

#### EVM (EIP-712) Example

```typescript
import { DutchOrderBuilder } from "@uniswap/uniswapx-sdk";
import { BigNumber } from "ethers5"; // ethers v5

const CHAIN_ID: number = <CHAIN_ID>;
const REACTOR_ADDRESS: string = <REACTOR_ADDRESS>;
const PERMIT2_ADDRESS: string = <PERMIT2_ADDRESS>;

async function signTypedQuote(quote: Quote) {
  const builder = new DutchOrderBuilder(
    CHAIN_ID,
    REACTOR_ADDRESS,
    PERMIT2_ADDRESS
  );
  const TOKEN_ADDRESS = <TOKEN_ADDRESS>;
  const PAIR_TOKEN_ADDRESS = <PAIR_TOKEN_ADDRESS>;

  const order = builder
    .deadline(Number(quote.deadline))
    .decayEndTime(Number(quote.deadline))
    .decayStartTime(Number(quote.deadline) - 100)
    .nonce(BigNumber.from(quote.relayer_nonce))
    .input({
      token: quote.type == "BUY" ? PAIR_TOKEN_ADDRESS : TOKEN_ADDRESS,
      startAmount:
        quote.type == "BUY"
          ? BigNumber.from(quote.pair_token_amount.toString())
          : BigNumber.from(quote.token_amount.toString()),
      endAmount:
        quote.type == "BUY"
          ? BigNumber.from(quote.pair_token_amount.toString())
          : BigNumber.from(quote.token_amount.toString()),
    })
    .output({
      token: quote.type == "BUY" ? TOKEN_ADDRESS : PAIR_TOKEN_ADDRESS,
      startAmount:
        quote.type == "BUY"
          ? BigNumber.from(quote.token_amount.toString())
          : BigNumber.from(quote.pair_token_amount.toString()),
      endAmount:
        quote.type == "BUY"
          ? BigNumber.from(quote.token_amount.toString())
          : BigNumber.from(quote.pair_token_amount.toString()),
      recipient: quote.user_address,
    })
    .swapper(quote.user_address)
    .exclusiveFiller(quote.merchant_address, BigNumber.from(0))
    .build();

  const { domain, types, values } = order.permitData();
  const primaryType: "PermitWitnessTransferFrom" = "PermitWitnessTransferFrom";
  const typedData = {
    domain,
    types,
    primaryType,
    message: values,
  };
  const signer = <SIGNER>;
  const signature = await signer.signTypedData(typedData);
  return signature;
}
```

#### Solana Example

```typescript
async function signTypedQuote(quote: Quote) {      
  const transaction = quote.transaction;
  if (!transaction) {
    throw new Error("No transaction to sign");
  }
  const versionedTransactions = VersionedTransaction.deserialize(
    Buffer.from(transaction, "hex")
  );
  versionedTransactions.sign([account]);

  const signerPubkeys =
    versionedTransactions.message.staticAccountKeys.slice(
      0,
      versionedTransactions.message.header.numRequiredSignatures
    );
  const publicKey = account.publicKey;
  const signerIndex = signerPubkeys.findIndex((pk) => pk.equals(publicKey));

  if (signerIndex === -1) {
    throw new Error("User's public key not found in required signers");
  }

  const _signature = versionedTransactions.signatures[
    signerIndex
  ] as Uint8Array;

  const signed = await Promise.resolve({
    signature: Buffer.from(_signature).toString("hex"),
    recentBlockhash:
      quote.recent_blockhash ??
      versionedTransactions.message.recentBlockhash,
    lastValidBlockHeight: quote.last_valid_block_height ?? 0,
  });

  signature = signed.signature;
  return signature;
}
```

### 3. Submit the Order

Once the quote is signed, submit it using the `/order` endpoint:

```
POST /order
```

#### Request Body

```typescript
{
 ...Quote, 
 "signature": string, // Quote signed by the user
}
```

#### Response (200 OK)

Upon success, the API will return the transaction hash confirming the execution of the trade.

```typescript
{
  "transaction_hash": string // Transaction hash of the fulfillment
  "transaction_hash_2"?: string // Optional: Transaction hash of the bridged fulfillment
}
```

> **Solana Note**: To be compatible with Lighthouse on Solana, you will need to provide `signedTransaction` in the request body as well:
>
> ```typescript
> const signedTransactionRaw = await signer.signTransaction(versionedTransactions);
> const signedTransaction = Buffer.from(signedTransactionRaw.serialize()).toString("hex");
> ```

## Integration Flow

### Complete Trading Flow

1. **Get Quote**: User requests a quote for buying/selling
2. **Review Quote**: Display pricing, fees, and slippage to user
3. **Approve Tokens**: (First time) User approves Permit2 or Universal program
4. **Sign Order**: User signs the quote using EIP-712 or Solana signing
5. **Submit Order**: Send signed quote to `/order` endpoint
6. **Confirm**: Merchant executes trade and returns transaction hash
7. **Verify**: Check transaction on block explorer

### Error Handling

The API returns standard HTTP status codes:

- **200**: Success
- **400**: Bad Request (invalid parameters)
- **401**: Unauthorized (invalid API key)
- **429**: Rate Limited
- **500**: Server Error

Error responses include a `message` field with details:

```typescript
{
  "error": "Invalid quote parameters",
  "message": "slippage_bips must be between 0 and 100"
}
```

## Rate Limits

- **Without API key**: 10 requests per minute
- **With API key**: 100 requests per minute
- **Custom limits**: Contact [partnerships team](../introduction/core-contributors.md)

## Best Practices

### Security

- Never expose API keys in client-side code
- Validate all user inputs before sending to API
- Verify transaction hashes on block explorers
- Use environment variables for sensitive data

### Performance

- Cache quote responses for a few seconds
- Implement request debouncing for quote updates
- Handle rate limits gracefully with exponential backoff

### User Experience

- Display all fees and prices clearly before signing
- Show estimated gas costs
- Provide transaction status updates
- Link to block explorer for confirmations

## Use Cases

### DEX Integration

Integrate uAssets into your decentralized exchange:

```typescript
// Get quote for user swap
const quote = await fetch('https://www.universal.xyz/api/v1/quote', {
  method: 'POST',
  body: JSON.stringify({
    type: 'BUY',
    token: 'BTC',
    pair_token: 'USDC',
    blockchain: 'BASE',
    slippage_bips: 20,
    user_address: userAddress,
    pair_token_amount: amountInWei,
  }),
});
```

### Wallet Integration

Enable native uAsset trading in wallets:

- Support 80+ cross-chain assets
- No bridges required
- Instant settlement

### dApp Integration

Add cross-chain asset support to your protocol:

- Expand available collateral
- Increase trading pairs
- Improve liquidity

## SDK Alternative

For a simpler integration, use the [Universal SDK](sdk.md) which handles signing, typing, and API calls automatically.

## Support

- [Discord Developer Chat](http://discord.gg/universalassets)
- [Contact Partnerships](../introduction/core-contributors.md)
- Email: dev@universal.xyz

## Related Resources

- [SDK Documentation](sdk.md)
- [Smart Contracts](smart-contracts.md)
- [Protocol Overview](protocol.md)
- [Referral Program](../trade/referrals.md)
