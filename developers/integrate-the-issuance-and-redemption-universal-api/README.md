# Integrate the Issuance & Redemption Universal API

## Getting Started

### Prerequisites

To use the Issuance & Redemption API, you need:

1. **API Key**: Contact dev@universal.xyz to request your partner API key. Include your organization name and use case in your request.
2. **EVM Wallet Address**: The wallet address you'll use for operations
3. **HTTPS Client**: Any HTTP client library (curl, axios, fetch, etc.)

### API Key Structure

Your API key follows this format:

```
<keyId>.<secret>
```

Example:

```
partner_abc123.sk_live_1234567890abcdef
```

* `keyId`: Public identifier for your API key
* `secret`: Private secret for authentication

> **Security**: Never expose your API key in client-side code or public repositories. Store it securely in environment variables or secret management systems.

### Authentication Flow

#### Step 1: Obtain JWT Token

Each JWT token is bound to a **single EVM wallet address**. If you need to operate with multiple wallet addresses, you must obtain separate JWT tokens for each address.

**Endpoint**: `POST https://api.universal.xyz/auth/token`

**Headers**:

```
X-API-Key: <keyId>.<secret>
Content-Type: application/json
```

**Request Body**:

```json
{
  "walletAddress": "0x742d35Cc6634C0532925a3b8D4C9db96C4b4d8b1"
}
```

**Response**:

```json
{
  "token": "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9...",
  "expiresAt": 1735689600
}
```

**cURL Example**:

```bash
curl -X POST https://api.universal.xyz/auth/token \
  -H "X-API-Key: partner_abc123.sk_live_1234567890abcdef" \
  -H "Content-Type: application/json" \
  -d '{"walletAddress": "0x742d35Cc6634C0532925a3b8D4C9db96C4b4d8b1"}'
```

#### Step 2: Use JWT Token

Include the JWT token in the `Authorization` header for all subsequent API requests:

```
Authorization: Bearer <token>
```

**Example**:

```bash
curl -X GET https://api.universal.xyz/issuance-redemption/mint/wallets \
  -H "Authorization: Bearer eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9..."
```

### Token Lifecycle

* **Expiration**: JWT tokens expire after 1 year (365 days)
* **Wallet Binding**: Each token is bound to the wallet address specified during issuance
* **Refresh**: Use the `/token/refresh` endpoint to renew an expiring token

#### Refreshing Tokens

**Endpoint**: `POST https://api.universal.xyz/auth/token/refresh`

```bash
curl -X POST https://api.universal.xyz/auth/token/refresh \
  -H "X-API-Key: partner_abc123.sk_live_1234567890abcdef" \
  -H "Content-Type: application/json" \
  -d '{"walletAddress": "0x742d35Cc6634C0532925a3b8D4C9db96C4b4d8b1"}'
```

### Multiple Wallet Addresses

**Important**: If you need to use different EVM wallet addresses, you must:

1. Obtain a new JWT token for each wallet address
2. Use the appropriate token when making requests for that specific wallet

**Example Workflow**:

```javascript
// Wallet 1
const token1 = await getJWTToken(apiKey, '0xWallet1...');
// Use token1 for operations with 0xWallet1

// Wallet 2
const token2 = await getJWTToken(apiKey, '0xWallet2...');
// Use token2 for operations with 0xWallet2
```

### Next Steps

* [Authentication Details ](authentication.md)- Deep dive into authentication
* [Mint API](mint-api.md) - Create deposit addresses and issue universal tokens
* [Burn API ](burn-api.md)- Redeem universal tokens to native chains
* [Examples](examples.md) - Complete code examples
