# Orders API

### Overview

The Orders API allows you to track the status of mint and burn operations. Each operation creates an order that can be queried to monitor progress and completion.

### Endpoints

#### Get Orders

Retrieves a paginated list of orders for the authenticated wallet.

**Endpoint**: `GET /orders`

**Headers**:

```
Authorization: Bearer <jwt_token>
```

**Query Parameters**:

* `limit` (integer, optional): Number of orders to return (default: 20, max: 100)
* `offset` (integer, optional): Number of orders to skip for pagination (default: 0)
* `type` (string, optional): Filter by order type (`mint` or `burn`)
* `status` (string, optional): Filter by order status (see statuses below)

**Response** (200 OK):

```json
{
  "success": true,
  "data": {
    "orders": [
      {
        "id": "12345",
        "type": "mint",
        "status": "completed",
        "sourceChain": "sol",
        "destinationChain": "base",
        "token": "SOL",
        "amount": "1500000000",
        "sourceWallet": "9WzDXwBbmkg8ZTbNMqUxvQRAyrZzDsGYdLVL9zYtAWWM",
        "destinationWallet": "0x742d35Cc6634C0532925a3b8D4C9db96C4b4d8b1",
        "workflowId": "mint-workflow-abc123",
        "transactionHash": "0xabcdef1234567890...",
        "errorMessage": null,
        "createdAt": "2025-01-15T10:30:00.000Z",
        "updatedAt": "2025-01-15T10:32:45.000Z"
      },
      {
        "id": "12344",
        "type": "burn",
        "status": "processing",
        "sourceChain": "base",
        "destinationChain": "xrp",
        "token": "uXRP",
        "amount": "5000000000000000000",
        "sourceWallet": "0x742d35Cc6634C0532925a3b8D4C9db96C4b4d8b1",
        "destinationWallet": "rN7n7otQDd6FczFgLdlqtyMVUXWqzp1GE1",
        "workflowId": "burn-workflow-xyz789",
        "transactionHash": null,
        "errorMessage": null,
        "createdAt": "2025-01-15T10:25:00.000Z",
        "updatedAt": "2025-01-15T10:26:30.000Z"
      }
    ],
    "pagination": {
      "limit": 20,
      "offset": 0,
      "total": 47,
      "hasMore": true
    }
  }
}
```

**cURL Example**:

```bash
curl -X GET "https://api.universal.xyz/issuance-redemption/orders?limit=10&type=mint&status=completed" \
  -H "Authorization: Bearer eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9..."
```

### Order Fields

#### Basic Information

* `id` (string): Unique order identifier
* `type` (string): Order type - `mint` or `burn`
* `status` (string): Current order status (see statuses below)
* `createdAt` (string): ISO 8601 timestamp of order creation
* `updatedAt` (string | null): ISO 8601 timestamp of last update

#### Chain and Token Details

* `sourceChain` (string): Chain where tokens are sent from
  * For mint: `sol`, `xrp`, `sui`, `doge`, `zec`
  * For burn: `base`, `arbitrum`, `katana`, `world`, `unichain`
* `destinationChain` (string): Chain where tokens are received
  * For mint: `base`, `arbitrum`, `katana`, `world`, `unichain`
  * For burn: `sol`, `xrp`, `sui`, `doge`, `zec`
* `token` (string): Token being transferred
  * For mint: `SOL`, `XRP`, `SUI`, `DOGE`, `ZEC`
  * For burn: `uSOL`, `uXRP`, `uSUI`, `uDOGE`, `uZEC`

#### Amount and Addresses

* `amount` (string): Amount in smallest unit (lamports, satoshis, wei, etc.)
* `sourceWallet` (string): Source wallet address
* `destinationWallet` (string): Destination wallet address

#### Processing Details

* `workflowId` (string): Temporal workflow ID for tracking internal processing
* `transactionHash` (string | null): Blockchain transaction hash (null if not yet processed)
* `errorMessage` (string | null): Error description if order failed (null otherwise)

### Order Statuses

| Status                | Description                                      | Next Step                     |
| --------------------- | ------------------------------------------------ | ----------------------------- |
| `pending`             | Order created, awaiting blockchain confirmation  | Wait for confirmation         |
| `processing`          | Blockchain deposit confirmed, workflow executing | Monitor for completion        |
| `minting` / `burning` | Actively minting or burning tokens               | Near completion               |
| `completed`           | Operation successfully completed                 | Check transactionHash         |
| `failed`              | Operation failed                                 | Review errorMessage           |
| `cancelled`           | Operation cancelled (manual intervention)        | Contact support if unexpected |

### Order Lifecycle

#### Mint Order Lifecycle

```mermaid
graph LR
    A[pending] --> B[processing]
    B --> C[minting]
    C --> D[completed]
    B --> E[failed]
    C --> E
```

1. **pending**: Deposit detected on source chain, awaiting confirmations
2. **processing**: Confirmations received, workflow initiated
3. **minting**: Minting universal tokens on destination EVM chain
4. **completed**: Universal tokens minted and transferred to destination wallet
5. **failed**: Error occurred during processing (see errorMessage)

#### Burn Order Lifecycle

```mermaid
graph LR
    A[pending] --> B[processing]
    B --> C[burning]
    C --> D[completed]
    B --> E[failed]
    C --> E
```

1. **pending**: Universal token transfer detected on EVM chain
2. **processing**: Workflow initiated for redemption
3. **burning**: Sending native tokens to destination address
4. **completed**: Native tokens sent to destination wallet
5. **failed**: Error occurred during processing (see errorMessage)

### Pagination

Use `limit` and `offset` for pagination:

```bash
# First page (orders 0-19)
curl "https://api.universal.xyz/issuance-redemption/orders?limit=20&offset=0"

# Second page (orders 20-39)
curl "https://api.universal.xyz/issuance-redemption/orders?limit=20&offset=20"

# Third page (orders 40-59)
curl "https://api.universal.xyz/issuance-redemption/orders?limit=20&offset=40"
```

**Pagination Response Fields**:

* `limit`: Requested limit
* `offset`: Current offset
* `total`: Total number of orders matching filters
* `hasMore`: Boolean indicating if more results exist

### Filtering

#### Filter by Type

```bash
# Only mint orders
curl "https://api.universal.xyz/issuance-redemption/orders?type=mint"

# Only burn orders
curl "https://api.universal.xyz/issuance-redemption/orders?type=burn"
```

#### Filter by Status

```bash
# Only completed orders
curl "https://api.universal.xyz/issuance-redemption/orders?status=completed"

# Only pending orders
curl "https://api.universal.xyz/issuance-redemption/orders?status=pending"

# Only failed orders
curl "https://api.universal.xyz/issuance-redemption/orders?status=failed"
```

#### Combine Filters

```bash
# Completed mint orders
curl "https://api.universal.xyz/issuance-redemption/orders?type=mint&status=completed&limit=50"
```

### Polling for Updates

To monitor order progress, poll the endpoint periodically:

```javascript
async function waitForOrderCompletion(orderId) {
  while (true) {
    const response = await fetch(`https://api.universal.xyz/issuance-redemption/orders?limit=100`, {
      headers: {
        Authorization: `Bearer ${jwtToken}`,
      },
    });

    const data = await response.json();
    const order = data.data.orders.find((o) => o.id === orderId);

    if (!order) {
      throw new Error('Order not found');
    }

    if (order.status === 'completed') {
      console.log('Order completed!', order.transactionHash);
      return order;
    }

    if (order.status === 'failed') {
      throw new Error(`Order failed: ${order.errorMessage}`);
    }

    // Poll every 10 seconds
    await new Promise((resolve) => setTimeout(resolve, 10000));
  }
}
```

**Recommended Polling Intervals**:

* Pending/Processing orders: Every 10-30 seconds
* Completed/Failed orders: No polling needed

### Error Messages

Common error messages in `errorMessage` field:

#### Mint Errors

| Error Message                     | Cause                          | Resolution                        |
| --------------------------------- | ------------------------------ | --------------------------------- |
| `Insufficient deposit amount`     | Amount below minimum threshold | Send more tokens                  |
| `Wallet address inactive`         | User wallet deactivated        | Contact support                   |
| `Blockchain confirmation timeout` | Source chain congestion        | Wait for retry or contact support |
| `Minting contract error`          | Smart contract issue           | Contact support                   |

#### Burn Errors

| Error Message                             | Cause                          | Resolution                       |
| ----------------------------------------- | ------------------------------ | -------------------------------- |
| `Insufficient balance`                    | Not enough uAssets in wallet   | Verify wallet balance            |
| `Amount below minimum burn`               | Amount below minimum threshold | Increase burn amount             |
| `Invalid destination address`             | Malformed destination address  | Update burn wallet configuration |
| `Transaction failed on destination chain` | Blockchain issue               | Contact support                  |

### Best Practices

1. **Poll Responsibly**: Don't poll more frequently than every 5-10 seconds
2. **Handle All Statuses**: Implement logic for all possible order statuses
3. **Store Order IDs**: Save order IDs for future reference and reconciliation
4. **Monitor Failed Orders**: Alert on failed orders and investigate errorMessage
5. **Use Pagination**: For wallets with many orders, paginate results efficiently
6. **Filter Appropriately**: Use filters to reduce response size and improve performance

### Next Steps

* [Mint API](mint-api.md) - Create mint operations that generate orders
* [Burn API](burn-api.md) - Create burn operations that generate orders
* [Examples](examples.md) - Complete order tracking examples
