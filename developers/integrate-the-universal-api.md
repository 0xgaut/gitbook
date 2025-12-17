---
description: >-
  Integrate just-in-time mint/redeems of 80+ uAssets directly in your app or
  protocol.
---

# Integrate the Universal API

The **Universal API** allows developers to seamlessly integrate **uAssets** into their applications and optionally earn fees. The API acts as an intermediary, connecting users with **Merchants** to facilitate buying, selling, and cross-chain transactions.

#### What is the Universal API?&#x20;

DeFi typically relies on liquidity pools (LPs) to provide assets for swaps. However, LPs are capital inefficient because liquidity sits unused until needed.

Universal solves this with JIT (just-in-time) Liquidity via the **Universal API** meaning:

* Instead of pre-depositing liquidity, Merchants mint and fulfill orders dynamically when demand arises.
* Liquidity is provided on an as-needed basis, optimizing capital efficiency.

{% hint style="info" %}
_To obtain API access with higher rate limits, please reach out to the Universal_ [_Partnerships_](partnerships.md) _team for an API key._
{% endhint %}

{% hint style="info" %}
_On EVM, the user will need to approve the Permit2 contract address `0x000000000022D473030F116dDEE9F6B43aC78BA3` to spend USDC or token address to be used for purchase, and the uAsset address for selling._

_On Solana, the user will need to create a token account for the uAsset they're acquiring and approve the Universal Solana program id:_ `3UcHkqbtMGtRZmWNGGfC6wwi9f7uGBacTBbuRgrGyoLG`


{% endhint %}

#### Monetization

Developers are able to add `referrer_address` and `referrer_bps` to the quote request to monetize their app. `referrer_bps` charge will be added on to every order, and the fees will be settled periodically (currently, at the end of every month) to `referrer_address`.&#x20;

### Typescript SDK Reference

The [viem](https://viem.sh) compatible Typescript SDK is the simplest way to integrate with the Universal API.&#x20;

<details>

<summary>Installation</summary>

Install the Universal SDK via npm:

```shellscript
npm install universal-sdk
```

</details>

<details>

<summary>Environment</summary>

For signing orders, you’ll need your private key. We strongly recommend storing private keys securely using environment variables. For example, create a .env file in your project root:

```env
PRIVATE_KEY=your_private_key_here
```

Load the .env file in your code by installing the dotenv package:

```shellscript
npm install dotenv
```

Then, in your project:&#x20;

```javascript
import dotenv from "dotenv";
dotenv.config();
```

</details>

<details>

<summary>Quick Start Example</summary>

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



</details>

### API Reference

<details>

<summary>Basics</summary>

### **API Basics**

#### **Base URL**

This is the base URL for all API requests.

```
https://relayer.universal.xyz/api (with an API key)
https://www.universal.xyz/api/v1 (without an API key)
```



#### **Authentication**

All API requests require authentication via a **Bearer Token**.

```
Authorization: Bearer <YOUR_API_KEY>
```

Ensure that your API key is included in every request to authenticate and authorize API usage.

***

###

</details>

<details>

<summary>Types</summary>

* `TokenName`: `BTC, SOL, XRP, DOGE, DOT, NEAR, LTC, ADA, BCH, ALGO, ...`
* `PairTokenName`: `USDC`&#x20;
* `BlockchainName`: `BASE | ARBITRUM | POLYGON | WORLD | SOLANA`
*   `Quote`

    ```typescript
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
    ```

</details>

<details>

<summary>Requesting a Quote</summary>

**Send a Quote Request**

To retrieve a **price quote** for a uAsset transaction, make a `POST` request to the following endpoint:

```
POST /quote
```

#### **Request Body Parameters**

* **`type`**: Specifies if the user wants to `BUY` or `SELL`.
* **`token`**: The uAsset being bought or sold.
* **`token_amount`**: (Optional) The amount of the asset being transacted.
* **`pair_token`**: The stable asset used for the trade (e.g., USDC).
* **`pair_token_amount`**: (Optional) The amount of the paired asset.
* **`slippage_bips`**: Slippage tolerance in basis points (0-100).
* **`blockchain`**: The blockchain network where the transaction is executed.
* **`user_address`**: The wallet address initiating the trade.
* `referrer_address` : (Optional) The address to send referrer fees to.&#x20;
* `referrer_bps`: (Optional) Basis points to charge the user as a referrer.

```
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

#### **Response (200 OK)**

Upon success, the API returns a quote containing:

* Quoted amounts
* Gas fees
* Merchant details
* A unique quote `ID` for later use

```
{
  ...Quote
}
```

</details>

<details>

<summary>Signing a Quote</summary>

#### **User Signs the Quote**

The user must sign the quote using **EIP-712 signature standards** before submitting the order.

#### **Example Code (Signing a Quote with EIP-712)**

```typescript
import { DutchOrderBuilder } from "@uniswap/uniswapx-sdk";
import { BigNumber } from "ethers5"; // ethers v5

type TokenName = "BTC" | "SOL" | "XRP" | "DOGE" | "DOT" | "NEAR" | "LTC" | "ADA" | "BCH" | "ALGO";
type PairTokenName = "USDC";
type BlockchainName = "BASE";

interface Quote {
  type: "BUY" | "SELL";
  token: TokenName;
  token_amount: bigint;
  pair_token: PairTokenName;
  slippage_bips: number;
  blockchain: BlockchainName;
  deadline: bigint;
  pair_token_amount: bigint;
  id: string;
  user_address: string;
  merchant_address: string;
  gas_fee_nominal: bigint;
  gas_fee_dollars: number;
  relayer_nonce: number;
}

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

**Example Code (Signing a Quote on Solana)**

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

</details>

<details>

<summary>Submitting an Order</summary>

**Submit a Signed Order**

Once the quote is signed, submit it using the `/order` endpoint:

```
POST /order
```

#### **Request Body Parameters**

This request includes the **signed quote** along with the necessary parameters from the previous steps.

```
{
 ...Quote, 
 "signature": string, // Quote signed by the user
}
```

**Response (200 OK)**

Upon success, the API will return the transaction hash confirming the execution of the trade.**Response**

```
{
  "transaction_hash": string // Transaction hash of the fulfillment
  (optional) "transaction_hash_2": string // Transaction hash of the bridged fulfillment
}
```

{% hint style="info" %}
To be compatible with Lighthouse on Solana, you will need to provide `signedTransaction` in the request body as well.&#x20;

```
const signedTransactionRaw = await signer.signTransaction(versionedTransactions);
const signedTransaction = Buffer.from(signedTransactionRaw.serialize()).toString("hex");
```
{% endhint %}

</details>
