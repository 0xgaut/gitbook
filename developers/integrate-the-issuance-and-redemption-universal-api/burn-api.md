# Burn API

### Overview

The Burn API enables you to convert universal EVM tokens (uSOL, uXRP, uSUI, uDOGE, uZEC) back into their native equivalents on the original chains (SOL, XRP, SUI, DOGE, ZEC).

### How Burning Works

1. **Create Burn Configuration**: Specify destination chain and address for receiving native tokens
2. **Receive Transfer Address**: The API returns the EVM address where you should send your universal tokens
3. **Transfer Universal Tokens**: Send uAssets to the provided address on the EVM chain
4. **Automatic Processing**: The system detects the transfer and automatically initiates redemption
5. **Receive Native Tokens**: Native tokens are sent to your specified destination address

> **⚠️ CRITICAL WARNING**: You MUST create a destination address configuration (Step 1) BEFORE sending any universal tokens to the merchant controller address. If you send tokens without first creating a burn configuration, your funds will remain locked at the merchant address and cannot be redeemed. Always call `POST /burn/wallets` first to set up your destination address, then send your tokens.

### Important Concepts

#### Wallet Address Correlation

All burn operations are correlated with the **EVM wallet address** specified in your JWT token:

* Burn configurations are associated with your EVM wallet
* The system tracks which universal tokens belong to which wallet
* To burn from a different EVM address, create a new JWT token for that address

#### Transfer Address

The **uAsset transfer address** (merchant address) is where you send your universal tokens for burning:

* This is a centralized merchant-controlled address
* **Same address for all users** (e.g., `0x5A6e781104b057C558f4CAed97915B67f5e71b78` on mainnet)
* The system identifies your transaction by your wallet address and routes accordingly
* Provided in the burn wallet creation response

### Endpoints

#### Create Burn Wallet Configuration

Creates a burn configuration specifying where to receive redeemed native tokens.

**Endpoint**: `POST /burn/wallets`

**Headers**:

```
Authorization: Bearer <jwt_token>
Content-Type: application/json
```

**Request Body**:

```json
{
  "destinationChain": "sol",
  "destinationAddress": "9WzDXwBbmkg8ZTbNMqUxvQRAyrZzDsGYdLVL9zYtAWWM",
  "memo": null
}
```

**Parameters**:

* `destinationChain` (string, required): Destination chain symbol
  * Production mainnet values: `sol`, `xrp`, `sui`, `doge`, `zec`
* `destinationAddress` (string, required): Your address on the destination chain where native tokens will be sent
* `memo` (string, optional): Destination tag/memo
  * **Required for XRP/TXRP**: Numeric destination tag (max 10 digits, max value: 4294967295)
  * Optional for other chains

**Response** (201 Created):

```json
{
  "success": true,
  "data": {
    "uAssetTransferAddress": "0x5A6e781104b057C558f4CAed97915B67f5e71b78",
    "destinationChain": "sol",
    "destinationAddress": "9WzDXwBbmkg8ZTbNMqUxvQRAyrZzDsGYdLVL9zYtAWWM",
    "memo": null
  }
}
```

**Response Fields**:

* `uAssetTransferAddress`: The EVM address where you should send universal tokens for burning
* `destinationChain`: The chain where you'll receive native tokens
* `destinationAddress`: Your address on the destination chain
* `memo`: Destination tag/memo (for XRP)

**cURL Example**:

```bash
curl -X POST https://api.universal.xyz/issuance-redemption/burn/wallets \
  -H "Authorization: Bearer eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9..." \
  -H "Content-Type: application/json" \
  -d '{
    "destinationChain": "sol",
    "destinationAddress": "9WzDXwBbmkg8ZTbNMqUxvQRAyrZzDsGYdLVL9zYtAWWM"
  }'
```

**XRP Example with Memo**:

```bash
curl -X POST https://api.universal.xyz/issuance-redemption/burn/wallets \
  -H "Authorization: Bearer eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9..." \
  -H "Content-Type: application/json" \
  -d '{
    "destinationChain": "xrp",
    "destinationAddress": "rN7n7otQDd6FczFgLdlqtyMVUXWqzp1GE1",
    "memo": "2740406962"
  }'
```

#### Get Burn Wallet Configurations

Retrieves all burn configurations for the authenticated wallet.

**Endpoint**: `GET /burn/wallets`

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
      "uAssetTransferAddress": "0x5A6e781104b057C558f4CAed97915B67f5e71b78",
      "destinationChain": "sol",
      "destinationAddress": "9WzDXwBbmkg8ZTbNMqUxvQRAyrZzDsGYdLVL9zYtAWWM",
      "memo": null
    },
    {
      "uAssetTransferAddress": "0x5A6e781104b057C558f4CAed97915B67f5e71b78",
      "destinationChain": "xrp",
      "destinationAddress": "rN7n7otQDd6FczFgLdlqtyMVUXWqzp1GE1",
      "memo": "2740406962"
    }
  ]
}
```

**cURL Example**:

```bash
curl -X GET https://api.universal.xyz/issuance-redemption/burn/wallets \
  -H "Authorization: Bearer eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9..."
```

#### Update Burn Destination Address

Updates the destination address for an existing burn configuration.

**Endpoint**: `PUT /burn/wallets`

**Headers**:

```
Authorization: Bearer <jwt_token>
Content-Type: application/json
```

**Request Body**:

```json
{
  "destinationChain": "sol",
  "destinationAddress": "5VzDXwBbmkg8ZTbNMqUxvQRAyrZzDsGYdLVL9zYtDEF",
  "memo": null
}
```

**Response** (200 OK):

```json
{
  "success": true,
  "data": {
    "uAssetTransferAddress": "0x5A6e781104b057C558f4CAed97915B67f5e71b78",
    "destinationChain": "sol",
    "destinationAddress": "5VzDXwBbmkg8ZTbNMqUxvQRAyrZzDsGYdLVL9zYtDEF",
    "memo": null
  }
}
```

### Supported Combinations

| Source Chain (EVM)                      | Universal Token | Destination Chains | Native Token |
| --------------------------------------- | --------------- | ------------------ | ------------ |
| base, arbitrum, katana, world, unichain | uSOL            | sol                | SOL          |
| base, arbitrum, katana, world, unichain | uXRP            | xrp                | XRP          |
| base, arbitrum, katana, world, unichain | uSUI            | sui                | SUI          |
| base, arbitrum, katana, world, unichain | uDOGE           | doge               | DOGE         |
| base, arbitrum, katana, world, unichain | uZEC            | zec                | ZEC          |

### Minimum Burn Amounts

| Universal Token | Minimum Burn Amount      | Decimal Value |
| --------------- | ------------------------ | ------------- |
| uSOL            | 1000000000000000000 wei  | 1.0 uSOL      |
| uXRP            | 25000000000000000000 wei | **25.0 uXRP** |
| uSUI            | 1000000000000000000 wei  | 1.0 uSUI      |
| uBTC            | 100000000000000 wei      | 0.0001 uBTC   |
| uDOGE           | 100000000000000000 wei   | 0.1 uDOGE     |
| uZEC            | 100000000000000000 wei   | 0.1 uZEC      |

> **Important**: XRP has a higher minimum redemption requirement of 25 XRP due to XRP Ledger reserve requirements.

### Fee Structure

#### Platform Fees

**Currently, all platform fees are 0% for all tokens.**

| Token | Platform Fee |
| ----- | ------------ |
| uSOL  | **0%**       |
| uXRP  | **0%**       |
| uSUI  | **0%**       |
| uDOGE | **0%**       |
| uZEC  | **0%**       |

> **Important**: The burn/redemption process is completely **free** from the customer perspective. The amount of universal tokens you burn equals the amount of native tokens you receive (1:1 conversion, excluding blockchain network fees which are covered by universal.xyz).

#### Gas Fees

All blockchain gas fees are covered by universal.xyz. Users do not pay any gas fees for burn operations.

### Chain-Specific Address Formats

#### Solana (`sol`)

* Format: Base58 encoded, 32-44 characters
* Example: `9WzDXwBbmkg8ZTbNMqUxvQRAyrZzDsGYdLVL9zYtAWWM`
* Validation: `^[1-9A-HJ-NP-Za-km-z]{32,44}$`

#### XRP Ledger (`xrp`)

* Format: Classic address starting with 'r', 25-34 characters
* Example: `rN7n7otQDd6FczFgLdlqtyMVUXWqzp1GE1`
* Validation: `^r[1-9A-HJ-NP-Za-km-z]{24,33}$`
* **Memo Required**: Numeric destination tag (1-10 digits, max: 4294967295)
* Memo Example: `2740406962`

#### Sui (`sui`)

* Format: Hex string with `0x` prefix, 66 characters total
* Example: `0x1234567890abcdef1234567890abcdef1234567890abcdef1234567890abcdef`
* Validation: `^0x[a-fA-F0-9]{64}$`

#### Dogecoin (`doge`)

* Format: Mainnet addresses start with 'D', 'A', or '9', 34 characters
* Example: `D7Z1n7FQKBTMhBqpL8xNjfR9VKXsCqHsKh`
* Validation: `^[DA9][1-9A-HJ-NP-Za-km-z]{33}$`

#### Zcash (`zec`)

* Format: Transparent addresses starting with `t1`, 35 characters
* Example: `t1abcdefghijklmnopqrstuvwxyz1234567`
* Validation: `^t1[1-9A-HJ-NP-Za-km-z]{33}$`
* **Note**: Shielded z-addresses are NOT supported

### Error Responses

#### Wallet Already Exists

```http
HTTP/1.1 400 Bad Request
Content-Type: application/json

{
  "success": false,
  "message": "Wallet already exists for this chain"
}
```

#### Invalid Destination Address

```http
HTTP/1.1 400 Bad Request
Content-Type: application/json

{
  "success": false,
  "message": "Invalid Solana address format. Must be base58 encoded, 32-44 characters."
}
```

#### Missing Memo for XRP

```http
HTTP/1.1 400 Bad Request
Content-Type: application/json

{
  "success": false,
  "message": "Memo is required for XRP and TXRP chains"
}
```

#### Invalid Memo Format

```http
HTTP/1.1 400 Bad Request
Content-Type: application/json

{
  "success": false,
  "message": "Memo must be numeric for XRP and TXRP chains"
}
```

#### Below Minimum Amount

```http
HTTP/1.1 400 Bad Request
Content-Type: application/json

{
  "success": false,
  "message": "Amount below minimum burn threshold"
}
```

### Workflow After Creating Burn Configuration

1. **Create Burn Configuration**: Set up destination address via `POST /burn/wallets`
2. **Note the Transfer Address**: Save the `uAssetTransferAddress` from the response
3. **Transfer Universal Tokens**: Send uAssets from your EVM wallet to the transfer address
4. **Automatic Detection**: The system monitors the blockchain and detects your transfer
5. **Automatic Redemption**: A Temporal workflow processes the burn and sends native tokens to your destination address (1:1 conversion - you receive exactly what you sent)
6. **Track Progress**: Use the Orders API to monitor the status

**Typical Timeline**:

* Base to SOL/XRP/SUI: \~2-5 minutes after confirmation
* Arbitrum to any: \~2-5 minutes after confirmation
* Base/Arbitrum to DOGE/ZEC: \~15-30 minutes (due to blockchain processing)

**Amount Received**:

* **1:1 Conversion**: If you burn 10 uSOL, you receive 10 SOL (no fees deducted)
* **No Platform Fees**: 0% platform fees on all tokens
* **No Gas Fees**: All blockchain fees covered by universal.xyz

### Best Practices

1. **Validate Addresses**: Ensure destination addresses are correctly formatted for the target chain
2. **Include Memo for XRP**: Always provide a valid numeric memo/destination tag for XRP burns
3. **Check Minimums**: Verify amounts meet minimum burn thresholds (especially 25 XRP minimum for uXRP)
4. **Update Carefully**: When updating destination addresses, ensure the new address is valid
5. **Monitor Orders**: Track burn operations through the Orders API
6. **1:1 Conversion**: Remember you receive exactly what you burn - no fees deducted

### Next Steps

* [Orders API](orders-api.md) - Track burn operation status
* [Mint API](mint-api.md) - Mint universal tokens from native tokens
* [Examples](examples.md) - Complete burn workflow examples
