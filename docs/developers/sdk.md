# TypeScript SDK

The Universal SDK is a [viem](https://viem.sh) compatible TypeScript library that simplifies integration with the Universal API.

## Overview

The TypeScript SDK provides a clean, typed interface for interacting with Universal Protocol, handling quote requests, order signing, and submission automatically.

### Why Use the SDK?

- **Type Safety**: Full TypeScript support with typed interfaces
- **Viem Compatible**: Works seamlessly with viem for wallet interactions
- **Simple API**: Abstracted complexity of signing and API calls
- **Production Ready**: Tested and maintained by Universal team

## Installation

Install the Universal SDK via npm:

```bash
npm install universal-sdk
```

### Dependencies

The SDK works with:

- `viem` - For wallet interactions and signing
- `dotenv` (optional) - For environment variable management

```bash
npm install viem dotenv
```

## Quick Start

### 1. Setup Environment

Store your private key securely using environment variables:

**.env**

```env
PRIVATE_KEY=your_private_key_here
```

**app.ts**

```typescript
import dotenv from "dotenv";
dotenv.config();
```

> **Security**: Never commit private keys or expose them in client-side code. Use environment variables or secure key management systems.

### 2. Complete Example

Below is a complete TypeScript example that shows you how to use the SDK to request a quote, generate EIP‑712 typed data, sign the data, and submit an order:

```typescript
import {
  UniversalRelayerSDK,
  generateTypedData,
  QuoteRequest,
} from "universal-sdk";
import { privateKeyToAccount } from "viem/accounts";
import { parseUnits } from "viem";
import dotenv from "dotenv";

dotenv.config();

async function executeOrder() {
  // Initialize the SDK (pass your API key if required)
  const universal = new UniversalRelayerSDK();

  // Create an account from your private key
  const account = privateKeyToAccount(process.env.PRIVATE_KEY as `0x${string}`);

  // Configure your quote parameters
  const quoteRequest: QuoteRequest = {
    type: "BUY",
    token: "BTC",
    pair_token: "USDC",
    blockchain: "BASE",
    slippage_bips: 20,
    user_address: account.address,
    pair_token_amount: parseUnits("5", 6).toString(), // 5 USDC, using 6 decimals
  };

  // Request a quote from the API
  const quote = await universal.getQuote(quoteRequest);
  console.log("Quote response:", quote);

  // Generate typed data as per EIP‑712 for signing
  const { typedData } = await generateTypedData(quote);

  // Sign the typed data using your account
  const signature = await account.signTypedData(typedData);

  // Combine the quote with its signature to form an order request
  const orderRequest = {
    ...quote,
    signature,
  };

  // Submit the order
  const orderResponse = await universal.submitOrder(orderRequest);
  console.log("Order response:", orderResponse);
}

executeOrder()
  .then(() => console.log("Order executed successfully"))
  .catch((error) => console.error("Error executing order:", error));
```

## API Reference

### UniversalRelayerSDK

Main SDK class for interacting with Universal API.

#### Constructor

```typescript
new UniversalRelayerSDK(config?: SDKConfig)
```

**Parameters:**

```typescript
interface SDKConfig {
  apiKey?: string;          // Optional API key for higher rate limits
  baseURL?: string;         // Optional custom base URL
}
```

**Example:**

```typescript
// Without API key
const universal = new UniversalRelayerSDK();

// With API key
const universal = new UniversalRelayerSDK({
  apiKey: process.env.UNIVERSAL_API_KEY
});
```

#### getQuote()

Request a price quote for a uAsset transaction.

```typescript
async getQuote(request: QuoteRequest): Promise<Quote>
```

**Parameters:**

```typescript
interface QuoteRequest {
  type: "BUY" | "SELL";
  token: string;                    // Token symbol (e.g., "BTC", "SOL")
  pair_token: string;               // Usually "USDC"
  blockchain: string;               // "BASE" | "ARBITRUM" | "POLYGON" | "WORLD" | "SOLANA"
  slippage_bips: number;            // 0-100 (basis points)
  user_address: string;             // User's wallet address
  token_amount?: string;            // Optional: amount of token (in wei/lamport)
  pair_token_amount?: string;       // Optional: amount of pair token (in wei/lamport)
  referrer_address?: string;        // Optional: referrer address for fees
  referrer_bps?: string;            // Optional: referrer fee in basis points
}
```

**Returns:**

```typescript
interface Quote {
  type: "BUY" | "SELL";
  token: string;
  token_amount: string;
  pair_token: string;
  slippage_bips: number;
  blockchain: string;
  deadline: string;
  pair_token_amount: string;
  id: string;
  user_address: string;
  merchant_address: string;
  gas_fee_nominal: string;
  gas_fee_dollars: number;
  relayer_nonce: number;
  merchant_id: string;
  mode: "DIRECT" | "BRIDGED";
}
```

**Example:**

```typescript
const quote = await universal.getQuote({
  type: "BUY",
  token: "BTC",
  pair_token: "USDC",
  blockchain: "BASE",
  slippage_bips: 20,
  user_address: "0x...",
  pair_token_amount: parseUnits("100", 6).toString(),
});
```

#### submitOrder()

Submit a signed order for execution.

```typescript
async submitOrder(order: SignedOrder): Promise<OrderResponse>
```

**Parameters:**

```typescript
interface SignedOrder extends Quote {
  signature: string;  // EIP-712 signature from user
}
```

**Returns:**

```typescript
interface OrderResponse {
  transaction_hash: string;      // Transaction hash of fulfillment
  transaction_hash_2?: string;   // Optional: bridged fulfillment tx hash
}
```

**Example:**

```typescript
const orderResponse = await universal.submitOrder({
  ...quote,
  signature: await account.signTypedData(typedData),
});
```

### generateTypedData()

Generate EIP-712 typed data for signing a quote.

```typescript
async generateTypedData(quote: Quote): Promise<TypedDataResult>
```

**Parameters:**

- `quote`: Quote object returned from `getQuote()`

**Returns:**

```typescript
interface TypedDataResult {
  typedData: {
    domain: any;
    types: any;
    primaryType: string;
    message: any;
  };
}
```

**Example:**

```typescript
const { typedData } = await generateTypedData(quote);
const signature = await account.signTypedData(typedData);
```

## Advanced Usage

### With Custom API Key

```typescript
const universal = new UniversalRelayerSDK({
  apiKey: process.env.UNIVERSAL_API_KEY,
});
```

### Selling uAssets

```typescript
const sellQuote = await universal.getQuote({
  type: "SELL",
  token: "BTC",
  pair_token: "USDC",
  blockchain: "BASE",
  slippage_bips: 20,
  user_address: account.address,
  token_amount: parseUnits("0.1", 8).toString(), // 0.1 BTC (8 decimals)
});
```

### With Referral Fees

```typescript
const quoteWithReferral = await universal.getQuote({
  type: "BUY",
  token: "SOL",
  pair_token: "USDC",
  blockchain: "SOLANA",
  slippage_bips: 20,
  user_address: account.address,
  pair_token_amount: parseUnits("50", 6).toString(),
  referrer_address: "0x...", // Your referrer address
  referrer_bps: "10",         // 0.10% fee
});
```

### Error Handling

```typescript
try {
  const quote = await universal.getQuote(quoteRequest);
  const { typedData } = await generateTypedData(quote);
  const signature = await account.signTypedData(typedData);
  const order = await universal.submitOrder({ ...quote, signature });
  
  console.log("Order successful:", order.transaction_hash);
} catch (error) {
  if (error.response?.status === 429) {
    console.error("Rate limited. Please try again later.");
  } else if (error.response?.status === 400) {
    console.error("Invalid request parameters:", error.response.data);
  } else {
    console.error("Order failed:", error.message);
  }
}
```

## Integration Examples

### React Hook

```typescript
import { useState } from 'react';
import { UniversalRelayerSDK, generateTypedData } from 'universal-sdk';
import { useAccount } from 'wagmi';

export function useUniversalTrade() {
  const { address } = useAccount();
  const [loading, setLoading] = useState(false);
  
  const executeTrade = async (params) => {
    try {
      setLoading(true);
      const universal = new UniversalRelayerSDK();
      
      const quote = await universal.getQuote({
        ...params,
        user_address: address,
      });
      
      const { typedData } = await generateTypedData(quote);
      const signature = await signTypedData(typedData);
      
      const result = await universal.submitOrder({
        ...quote,
        signature,
      });
      
      return result;
    } finally {
      setLoading(false);
    }
  };
  
  return { executeTrade, loading };
}
```

### Node.js Script

```typescript
import { UniversalRelayerSDK, generateTypedData } from "universal-sdk";
import { createWalletClient, http } from "viem";
import { privateKeyToAccount } from "viem/accounts";
import { base } from "viem/chains";

async function tradeBTC() {
  const account = privateKeyToAccount(process.env.PRIVATE_KEY);
  const universal = new UniversalRelayerSDK();

  // Get quote
  const quote = await universal.getQuote({
    type: "BUY",
    token: "BTC",
    pair_token: "USDC",
    blockchain: "BASE",
    slippage_bips: 20,
    user_address: account.address,
    pair_token_amount: "1000000", // 1 USDC
  });

  // Sign and submit
  const { typedData } = await generateTypedData(quote);
  const signature = await account.signTypedData(typedData);
  const result = await universal.submitOrder({ ...quote, signature });

  console.log("Trade executed:", result.transaction_hash);
}

tradeBTC();
```

## Support

- [Discord Developer Chat](http://discord.gg/universalassets)
- [API Documentation](api.md)
- Email: dev@universal.xyz

## Related Resources

- [API Reference](api.md)
- [Smart Contracts](smart-contracts.md)
- [Protocol Overview](protocol.md)
