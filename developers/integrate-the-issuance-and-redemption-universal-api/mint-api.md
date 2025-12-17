# Mint API

### Overview

The Mint API enables you to convert native tokens (SOL, XRP, SUI, DOGE, ZEC) into their universal EVM equivalents (uSOL, uXRP, uSUI, uDOGE, uZEC) on supported EVM chains.

### How Minting Works

1. **Create Deposit Address**: Request a deposit address for a specific token and destination chain
2. **Receive Deposit Address**: The API returns a unique deposit address
3. **Send Tokens**: Transfer native tokens to the deposit address
4. **Automatic Processing**: The system detects the deposit and automatically mints universal tokens to your EVM wallet
5. **Track Status**: Monitor the operation through the Orders API

**1:1 Conversion - Zero Fees**: The amount of native tokens you send equals the amount of universal tokens you receive. If you send 10 SOL, you receive 10 uSOL. No platform fees, no gas fees - completely free.

### Important Concepts

#### Deposit Address Reusability

**Key Point**: Deposit addresses for mint operations can be **reused without additional API interaction**.

* Once created, a deposit address remains valid for the specific token and destination chain combination
* You can send multiple deposits to the same address
* Each deposit automatically triggers a new mint workflow
* No need to create a new deposit address for subsequent mints

**Example**: If you create an XRP deposit address for minting to Base:

* The XRP address remains the same for all future mints
* Simply send XRP to that address whenever you want to mint uXRP on Base
* The API automatically detects each new deposit and processes it

#### Wallet Address Correlation

All mint operations are correlated with the **EVM wallet address** specified in your JWT token:

* Universal tokens (uSOL, uXRP, etc.) are minted to this EVM address
* All deposit addresses created with a specific JWT token are associated with that wallet
* To mint to a different EVM address, create a new JWT token for that address

### Endpoints

#### Create Mint Wallet (Deposit Address)

Creates a new deposit address for receiving native tokens.

**Endpoint**: `POST /mint/wallets`

**Headers**:

```
Authorization: Bearer <jwt_token>
Content-Type: application/json
```

**Request Body**:

```json
{
  "chain": "sol",
  "destinationChain": "base"
}
```

**Parameters**:

* `chain` (string, required): Source chain symbol
  * Valid values: `sol`, `xrp`, `sui`, `doge`, `zec`
* `destinationChain` (string, required): Destination EVM chain symbol
  * Valid values: `base`, `arbitrum`, `katana`, `world`, `unichain`

**Response** (201 Created):

```json
{
  "success": true,
  "data": {
    "address": "9WzDXwBbmkg8ZTbNMqUxvQRAyrZzDsGYdLVL9zYtAWWM",
    "chain": "sol",
    "destinationChain": "base"
  }
}
```

**Response Fields**:

* `address`: The deposit address where you should send native tokens
* `chain`: Source chain symbol
* `destinationChain`: Destination EVM chain symbol

**cURL Example**:

```bash
curl -X POST https://api.universal.xyz/issuance-redemption/mint/wallets \
  -H "Authorization: Bearer eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9..." \
  -H "Content-Type: application/json" \
  -d '{
    "chain": "sol",
    "destinationChain": "base"
  }'
```

#### Get Mint Wallets

Retrieves all deposit addresses created for the authenticated wallet.

**Endpoint**: `GET /mint/wallets`

**Headers**:

```
Authorization: Bearer <jwt_token>
```

**Response** (200 OK):

```json
{
  "success": true,
  "data": [
    {
      "address": "9WzDXwBbmkg8ZTbNMqUxvQRAyrZzDsGYdLVL9zYtAWWM",
      "chain": "sol",
      "destinationChain": "base"
    },
    {
      "address": "rN7n7otQDd6FczFgLdlqtyMVUXWqzp1GE1",
      "chain": "xrp",
      "destinationChain": "arbitrum"
    }
  ]
}
```

**cURL Example**:

```bash
curl -X GET https://api.universal.xyz/issuance-redemption/mint/wallets \
  -H "Authorization: Bearer eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9..."
```

### Supported Combinations

#### Production Mainnet Only

| Source Chain | Token | Destination Chains                      | Universal Token |
| ------------ | ----- | --------------------------------------- | --------------- |
| `sol`        | SOL   | base, arbitrum, katana, world, unichain | uSOL            |
| `xrp`        | XRP   | base, arbitrum, katana, world, unichain | uXRP            |
| `sui`        | SUI   | base, arbitrum, katana, world, unichain | uSUI            |
| `doge`       | DOGE  | base, arbitrum, katana, world, unichain | uDOGE           |
| `zec`        | ZEC   | base, arbitrum, katana, world, unichain | uZEC            |

### Chain-Specific Details

#### XRP Ledger

XRP uses a **shared wallet architecture** with individual destination tags:

* The API provides a unique XRP address for your mints
* Internally, a shared wallet is used for operational efficiency
* Each user gets a unique address generated from the shared wallet
* This address can be reused for all future XRP mints to the same destination chain
* **No memo/destination tag required** - your unique address handles routing

**Example**:

```json
{
  "address": "rN7n7otQDd6FczFgLdlqtyMVUXWqzp1GE1",
  "chain": "xrp",
  "destinationChain": "base"
}
```

Simply send XRP to this address - the system automatically routes it correctly.

#### Solana

* Standard Solana addresses (base58 encoded, 32-44 characters)
* Minimum deposit typically 0.01 SOL (dust threshold)
* Address format: `9WzDXwBbmkg8ZTbNMqUxvQRAyrZzDsGYdLVL9zYtAWWM`

#### Sui

* Hex addresses with `0x` prefix (66 characters total)
* Address format: `0x1234567890abcdef1234567890abcdef1234567890abcdef1234567890abcdef`

#### Dogecoin

* Mainnet addresses start with 'D', 'A', or '9'
* Address format: `D1234567890abcdefghijklmnopqrstuvwx`

#### Zcash

* Transparent addresses only (shielded z-addresses not supported)
* Mainnet addresses start with `t1`
* Address format: `t1abcdefghijklmnopqrstuvwxyz1234567`

### Error Responses

#### Wallet Already Exists

```http
HTTP/1.1 400 Bad Request
Content-Type: application/json

{
  "success": false,
  "message": "A wallet already exists for this chain and destination combination"
}
```

**Resolution**: Use the existing deposit address retrieved via `GET /mint/wallets`

#### Invalid Chain

```http
HTTP/1.1 400 Bad Request
Content-Type: application/json

{
  "success": false,
  "message": "Unsupported source chain"
}
```

#### Invalid Destination Chain

```http
HTTP/1.1 400 Bad Request
Content-Type: application/json

{
  "success": false,
  "message": "Unsupported destination chain"
}
```

#### Unauthorized

```http
HTTP/1.1 401 Unauthorized
Content-Type: application/json

{
  "success": false,
  "message": "Invalid or expired token"
}
```

### Workflow After Creating Deposit Address

1. **Store the Deposit Address**: Save the returned address for future use
2. **Send Tokens**: Transfer native tokens from your wallet to the deposit address
3. **Automatic Detection**: The system monitors the blockchain and detects your deposit
4. **Automatic Minting**: A Temporal workflow automatically mints universal tokens to your EVM wallet (1:1 conversion - you receive exactly what you sent)
5. **Track Progress**: Use the Orders API to monitor the status

**Typical Timeline**:

* Solana: \~1-2 minutes after confirmation
* XRP: \~1-2 minutes after confirmation
* Sui: \~1-2 minutes after confirmation
* Dogecoin: \~30-60 minutes (due to confirmation requirements)
* Zcash: \~30-60 minutes (due to confirmation requirements)

**Amount Received**:

* **1:1 Conversion**: If you send 5 SOL, you receive 5 uSOL (no fees deducted)
* **No Platform Fees**: 0% platform fees on all tokens
* **No Gas Fees**: All blockchain fees covered by universal.xyz

### Minimum Deposit Amounts

| Token | Minimum Deposit |
| ----- | --------------- |
| SOL   | 0.01 SOL        |
| XRP   | 1 XRP           |
| SUI   | 1 SUI           |
| DOGE  | 10 DOGE         |
| ZEC   | 0.001 ZEC       |

Deposits below these thresholds may not be processed.

### Best Practices

1. **Create Once, Use Many**: Create deposit addresses once and reuse them for all future mints
2. **Check Existing Wallets**: Before creating a new deposit address, check if one already exists for that combination
3. **Store Addresses Securely**: Save deposit addresses in your system for easy reference
4. **Monitor Orders**: Track mint operations through the Orders API
5. **Respect Minimums**: Ensure deposits meet minimum thresholds
6. **Network Confirmations**: Wait for sufficient confirmations before expecting processing
7. **1:1 Conversion**: Remember you receive exactly what you send - no fees deducted

### Next Steps

* [Burn API](burn-api.md) - Redeem universal tokens back to native tokens
* [Orders API ](orders-api.md)- Track mint operation status
* [Examples](examples.md) - Complete mint workflow examples
