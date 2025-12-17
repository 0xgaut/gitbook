# Error Handling

### Overview

The FastMint and Burn API uses standard HTTP status codes and consistent JSON error responses to indicate the success or failure of requests.

### Response Format

#### Success Response

```json
{
  "success": true,
  "data": {
    // Response data
  }
}
```

#### Error Response

```json
{
  "success": false,
  "message": "Human-readable error message",
  "error": {
    // Optional additional error details
  }
}
```

### HTTP Status Codes

| Status Code | Meaning               | Common Causes                                               |
| ----------- | --------------------- | ----------------------------------------------------------- |
| 200         | OK                    | Successful GET request                                      |
| 201         | Created               | Successful POST request creating a resource                 |
| 400         | Bad Request           | Invalid request parameters or body                          |
| 401         | Unauthorized          | Missing, invalid, or expired JWT token                      |
| 403         | Forbidden             | Valid token but insufficient permissions or inactive wallet |
| 404         | Not Found             | Resource or endpoint not found                              |
| 500         | Internal Server Error | Server-side error                                           |

### Common Error Responses

#### Authentication Errors

**Missing API Key**

```http
HTTP/1.1 401 Unauthorized
Content-Type: application/json

{
  "success": false,
  "message": "Missing or malformed X-API-Key"
}
```

**Cause**: `X-API-Key` header not provided or incorrectly formatted

**Resolution**: Include `X-API-Key: <keyId>.<secret>` header

**Invalid API Key**

```http
HTTP/1.1 403 Forbidden
Content-Type: application/json

{
  "success": false,
  "message": "Invalid API key"
}
```

**Cause**: API key not found, inactive, or expired

**Resolution**: Verify API key is correct and active; contact support if needed

**Missing JWT Token**

```http
HTTP/1.1 401 Unauthorized
Content-Type: application/json

{
  "success": false,
  "message": "Missing authorization header"
}
```

**Cause**: `Authorization` header not provided

**Resolution**: Include `Authorization: Bearer <token>` header

**Invalid JWT Token**

```http
HTTP/1.1 403 Forbidden
Content-Type: application/json

{
  "success": false,
  "message": "Invalid token signature"
}
```

**Cause**: Token signature invalid, token tampered with, or wrong issuer

**Resolution**: Obtain a new token via `/token`

**Expired JWT Token**

```http
HTTP/1.1 401 Unauthorized
Content-Type: application/json

{
  "success": false,
  "message": "Token expired"
}
```

**Cause**: Token expiration time (`exp` claim) has passed

**Resolution**: Refresh token via `/token/refresh` or issue new token

**Inactive Wallet**

```http
HTTP/1.1 403 Forbidden
Content-Type: application/json

{
  "success": false,
  "message": "Wallet is not active"
}
```

**Cause**: User wallet has been deactivated

**Resolution**: Contact support to reactivate wallet

#### Mint API Errors

**Wallet Already Exists**

```http
HTTP/1.1 400 Bad Request
Content-Type: application/json

{
  "success": false,
  "message": "A wallet already exists for this chain and destination combination"
}
```

**Cause**: Attempting to create duplicate deposit address for same chain/destination pair

**Resolution**: Use existing wallet from `GET /mint/wallets`

**Invalid Source Chain**

```http
HTTP/1.1 400 Bad Request
Content-Type: application/json

{
  "success": false,
  "message": "Unsupported source chain"
}
```

**Cause**: Invalid chain symbol

**Resolution**: Use valid chain: `sol`, `xrp`, `sui`, `doge`, `zec`

**Invalid Destination Chain**

```http
HTTP/1.1 400 Bad Request
Content-Type: application/json

{
  "success": false,
  "message": "Unsupported destination chain"
}
```

**Cause**: Invalid chain symbol

**Resolution**: Use valid chain: `base`, `arbitrum`, `katana`, `world`, `unichain`

#### Burn API Errors

**Burn Wallet Already Exists**

```http
HTTP/1.1 400 Bad Request
Content-Type: application/json

{
  "success": false,
  "message": "Wallet already exists for this chain"
}
```

**Cause**: Attempting to create duplicate burn configuration for same destination chain

**Resolution**: Update existing configuration via `PUT /burn/wallets` or use existing one

**Invalid Destination Address Format**

```http
HTTP/1.1 400 Bad Request
Content-Type: application/json

{
  "success": false,
  "message": "Invalid Solana address format. Must be base58 encoded, 32-44 characters."
}
```

**Cause**: Destination address doesn't match expected format for the chain

**Resolution**: Verify address format matches chain requirements:

* Solana: base58, 32-44 chars
* XRP: starts with 'r', 25-34 chars
* Sui: hex with `0x` prefix, 66 chars
* Dogecoin: starts with 'D', 'A', or '9', 34 chars
* Zcash: starts with `t1`, 35 chars

**Missing XRP Memo**

```http
HTTP/1.1 400 Bad Request
Content-Type: application/json

{
  "success": false,
  "message": "Memo is required for XRP and TXRP chains"
}
```

**Cause**: Memo not provided for XRP destination

**Resolution**: Include numeric `memo` field (1-10 digits, max: 4294967295)

**Invalid XRP Memo Format**

```http
HTTP/1.1 400 Bad Request
Content-Type: application/json

{
  "success": false,
  "message": "Memo must be numeric for XRP and TXRP chains"
}
```

**Cause**: Memo contains non-numeric characters

**Resolution**: Use only numeric digits (0-9) in memo

**XRP Memo Too Large**

```http
HTTP/1.1 400 Bad Request
Content-Type: application/json

{
  "success": false,
  "message": "Memo must not exceed maximum value (4294967295)"
}
```

**Cause**: Memo exceeds 32-bit unsigned integer limit

**Resolution**: Use memo value ≤ 4294967295

**Amount Below Minimum**

```http
HTTP/1.1 400 Bad Request
Content-Type: application/json

{
  "success": false,
  "message": "Amount below minimum burn threshold"
}
```

**Cause**: Burn amount below minimum for the token

**Resolution**: Ensure amount meets minimum:

* uSOL, uXRP, uSUI: 1.0 tokens
* uBTC: 0.0001 tokens
* uDOGE, uZEC: 0.1 tokens

#### Order API Errors

**Failed to Retrieve Orders**

```http
HTTP/1.1 500 Internal Server Error
Content-Type: application/json

{
  "success": false,
  "message": "Failed to retrieve orders"
}
```

**Cause**: Database or internal error

**Resolution**: Retry request; contact support if persists

#### Validation Errors

**Invalid Request Body**

```http
HTTP/1.1 400 Bad Request
Content-Type: application/json

{
  "success": false,
  "message": "Invalid request body",
  "error": {
    "issues": [
      {
        "path": ["destinationChain"],
        "message": "Invalid enum value. Expected 'sol' | 'xrp' | 'sui' | 'doge' | 'zec'"
      }
    ]
  }
}
```

**Cause**: Request body fails Zod schema validation

**Resolution**: Check `error.issues` for specific field errors

**Missing Required Field**

```http
HTTP/1.1 400 Bad Request
Content-Type: application/json

{
  "success": false,
  "message": "Validation error",
  "error": {
    "issues": [
      {
        "path": ["walletAddress"],
        "message": "Required"
      }
    ]
  }
}
```

**Cause**: Required field missing from request

**Resolution**: Include all required fields

### Error Handling Best Practices

#### 1. Implement Retry Logic

```javascript
async function makeRequestWithRetry(url, options, maxRetries = 3) {
  for (let i = 0; i < maxRetries; i++) {
    try {
      const response = await fetch(url, options);

      // Don't retry client errors (4xx)
      if (response.status >= 400 && response.status < 500) {
        throw new Error(await response.text());
      }

      // Retry server errors (5xx)
      if (response.status >= 500) {
        if (i === maxRetries - 1) throw new Error('Max retries exceeded');
        await sleep(Math.pow(2, i) * 1000); // Exponential backoff
        continue;
      }

      return await response.json();
    } catch (error) {
      if (i === maxRetries - 1) throw error;
    }
  }
}
```

#### 2. Handle Token Expiration

```javascript
async function makeAuthenticatedRequest(url, token) {
  const response = await fetch(url, {
    headers: {
      Authorization: `Bearer ${token}`,
    },
  });

  if (response.status === 401) {
    // Token expired, refresh and retry
    const newToken = await refreshToken();
    return makeAuthenticatedRequest(url, newToken);
  }

  return response.json();
}
```

#### 3. Validate Before Sending

```javascript
async function createMintWallet(chain, destinationChain) {
  // Validate against config first
  const config = await fetch('https://api.universal.xyz/issuance-redemption/config/mint').then((r) => r.json());

  const validSource = config.data.sourceChains.some((c) => c.symbol === chain && c.type === 'main');

  const validDestination = config.data.destinationChains.some(
    (c) => c.symbol === destinationChain && c.type === 'main',
  );

  if (!validSource || !validDestination) {
    throw new Error('Invalid chain combination');
  }

  // Proceed with request
  return fetch('/mint/wallets', {
    method: 'POST',
    headers: {
      Authorization: `Bearer ${token}`,
      'Content-Type': 'application/json',
    },
    body: JSON.stringify({ chain, destinationChain }),
  });
}
```

#### 4. Log Errors Properly

```javascript
async function handleApiError(error, context) {
  const errorLog = {
    timestamp: new Date().toISOString(),
    context,
    status: error.status,
    message: error.message,
    details: error.error,
  };

  // Log to your monitoring system
  console.error('API Error:', JSON.stringify(errorLog));

  // Alert on specific errors
  if (error.status === 403 && error.message.includes('Wallet is not active')) {
    await alertSupport('Wallet deactivation detected', errorLog);
  }
}
```

#### 5. Graceful Degradation

```javascript
async function getMintWallets(token) {
  try {
    const response = await fetch('/mint/wallets', {
      headers: { Authorization: `Bearer ${token}` },
    });

    if (!response.ok) {
      throw new Error('Failed to fetch wallets');
    }

    return await response.json();
  } catch (error) {
    // Return cached data or empty array on error
    console.error('Error fetching wallets:', error);
    return { success: false, data: [] };
  }
}
```

### Rate Limiting

While not currently enforced, be mindful of request frequency:

* **Token Issuance**: Cache tokens and reuse; don't issue new tokens for every request
* **Orders Polling**: Poll every 10-30 seconds, not more frequently
* **Configuration**: Cache config responses for hours, not minutes

### Support and Troubleshooting

If you encounter persistent errors:

1. **Check Status Page**: Verify API availability
2. **Review Logs**: Check error messages and status codes
3. **Validate Input**: Ensure request format matches documentation
4. **Contact Support**: Email dev@universal.xyz with:
   * Error message and status code
   * Request details (sanitize sensitive data)
   * Timestamp of error
   * Your partner ID

### Next Steps

* [Examples](examples.md) - See error handling in complete examples
* [Authentication](authentication.md) - Review authentication requirements
* [Mint API](mint-api.md) - Understand mint-specific errors
* [Burn API ](burn-api.md)- Understand burn-specific errors
