# Authentication

### Overview

The Issuance and Redemption API uses a two-step authentication process:

1. **API Key Authentication**: Your partner API key identifies your organization
2. **JWT Token Authentication**: JWT tokens authorize operations for specific wallet addresses

### API Key Authentication

#### API Key Format

```
<keyId>.<secret>
```

Components:

* **keyId**: Public identifier (e.g., `partner_abc123`)
* **secret**: Private secret (e.g., `sk_live_1234567890abcdef`)

#### Using API Keys

API keys are used **only** for JWT token issuance and refresh operations.

**Header Format**:

```
X-API-Key: <keyId>.<secret>
```

**Endpoints that accept API keys**:

* `POST /token` - Issue new JWT token (modern)
* `POST /token/refresh` - Refresh JWT token (modern)

#### Obtaining an API Key

To request a partner API key, contact dev@universal.xyz with:

* Your organization name
* Use case description
* Expected transaction volume
* Technical contact information

#### API Key Security

* Store API keys in environment variables or secret management systems
* Never commit API keys to version control
* Rotate API keys regularly

### JWT Token Authentication

#### Token Structure

JWT tokens are RS256-signed JSON Web Tokens containing:

```json
{
  "iss": "https://api.universal.xyz/auth",
  "aud": "https://api.universal.xyz",
  "sub": "0x742d35cc6634c0532925a3b8d4c9db96c4b4d8b1",
  "iat": 1704067200,
  "exp": 1735689600,
  "verified_credentials": [
    {
      "address": "0x742d35cc6634c0532925a3b8d4c9db96c4b4d8b1"
    }
  ],
  "azp": "550e8400-e29b-41d4-a716-446655440000",
  "https://universal.xyz/partner_id": "550e8400-e29b-41d4-a716-446655440000",
  "https://universal.xyz/type": "B2B"
}
```

**Key Claims**:

* `sub`: Wallet address (lowercase)
* `exp`: Expiration timestamp (1 year from issuance)
* `verified_credentials`: Array of verified wallet addresses
* `azp`: Partner ID
* `https://universal.xyz/partner_id`: Your partner identifier
* `https://universal.xyz/type`: User type (`B2B` for partners)

#### Obtaining JWT Tokens

**Request**:

```http
POST /token HTTP/1.1
Host: api.universal.xyz/auth
X-API-Key: partner_abc123.sk_live_1234567890abcdef
Content-Type: application/json

{
  "walletAddress": "0x742d35Cc6634C0532925a3b8D4C9db96C4b4d8b1"
}
```

**Response**:

```json
{
  "token": "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJhcGkudW5pdmVyc2FsLnh5ei9hdXRoIiwiYXVkIjoiYnJpZGdlLWFwaSIsInN1YiI6IjB4NzQyZDM1Y2M2NjM0YzA1MzI5MjVhM2I4ZDRjOWRiOTZjNGI0ZDhiMSIsImlhdCI6MTcwNDA2NzIwMCwiZXhwIjoxNzM1Njg5NjAwLCJ2ZXJpZmllZF9jcmVkZW50aWFscyI6W3siYWRkcmVzcyI6IjB4NzQyZDM1Y2M2NjM0YzA1MzI5MjVhM2I4ZDRjOWRiOTZjNGI0ZDhiMSJ9XSwidHlwZSI6IkIyQiIsImF6cCI6IjU1MGU4NDAwLWUyOWItNDFkNC1hNzE2LTQ0NjY1NTQ0MDAwMCIsImh0dHBzOi8vdW5pdmVyc2FsLnh5ei9jaGFubmVsIjoicGFydG5lci1hcGkiLCJodHRwczovL3VuaXZlcnNhbC54eXovcGFydG5lcl9pZCI6IjU1MGU4NDAwLWUyOWItNDFkNC1hNzE2LTQ0NjY1NTQ0MDAwMCJ9.signature",
  "expiresAt": 1735689600
}
```

#### Using JWT Tokens

Include the JWT token in the `Authorization` header for all API requests:

```http
Authorization: Bearer <token>
```

**Example**:

```http
GET /mint/wallets HTTP/1.1
Host: api.universal.xyz/issuance-redemption
Authorization: Bearer eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9...
```

#### Token Validation

The API validates:

* ✅ Token signature using RS256 algorithm
* ✅ Token expiration (`exp` claim)
* ✅ Issuer (`iss` claim)
* ✅ Audience (`aud` claim)
* ✅ Wallet address matches verified credentials
* ✅ User wallet status is active

#### Wallet Address Binding

**Important**: Each JWT token is bound to a specific wallet address. All operations performed with that token will be associated with the wallet address specified in the `sub` claim.

**To use multiple wallet addresses**:

```javascript
// Different tokens for different wallets
const token1 = await issueToken(apiKey, '0xWallet1');
const token2 = await issueToken(apiKey, '0xWallet2');

// Use token1 for wallet1 operations
await createMintWallet(token1, { chain: 'sol', destinationChain: 'base' });

// Use token2 for wallet2 operations
await createMintWallet(token2, { chain: 'xrp', destinationChain: 'arbitrum' });
```

#### Token Refresh

Refresh tokens before expiration to maintain uninterrupted access:

**Request**:

```http
POST /token/refresh HTTP/1.1
Host: api.universal.xyz/auth
X-API-Key: partner_abc123.sk_live_1234567890abcdef
Content-Type: application/json

{
  "walletAddress": "0x742d35Cc6634C0532925a3b8D4C9db96C4b4d8b1"
}
```

**Response**:

```json
{
  "token": "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9...",
  "expiresAt": 1767225600
}
```

### Error Responses

#### Invalid API Key

```http
HTTP/1.1 403 Forbidden
Content-Type: application/json

{
  "success": false,
  "message": "Invalid API key"
}
```

#### Malformed API Key

```http
HTTP/1.1 401 Unauthorized
Content-Type: application/json

{
  "success": false,
  "message": "Missing or malformed X-API-Key"
}
```

#### Expired JWT Token

```http
HTTP/1.1 401 Unauthorized
Content-Type: application/json

{
  "success": false,
  "message": "Token expired"
}
```

#### Invalid JWT Token

```http
HTTP/1.1 403 Forbidden
Content-Type: application/json

{
  "success": false,
  "message": "Invalid token signature"
}
```

#### Inactive Wallet

```http
HTTP/1.1 403 Forbidden
Content-Type: application/json

{
  "success": false,
  "message": "Wallet is not active"
}
```

### Best Practices

1. **Token Caching**: Cache JWT tokens and reuse them until near expiration
2. **Proactive Refresh**: Refresh tokens before they expire (e.g., when 90% of lifetime has elapsed)
3. **Error Handling**: Implement retry logic for 401/403 errors after token refresh
4. **Secure Storage**: Store tokens securely, never in client-side JavaScript
5. **HTTPS Only**: Always use HTTPS for API requests
6. **Wallet Management**: Track which token corresponds to which wallet address

### Next Steps

* [Mint API](mint-api.md) - Create deposit addresses and issue universal tokens
* [Burn API](burn-api.md) - Redeem universal tokens to native chains
* [Examples](examples.md) - Complete authentication examples
