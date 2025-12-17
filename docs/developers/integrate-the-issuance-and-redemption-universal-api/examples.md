# Examples

### Overview

This page provides complete, working examples for common FastMint and Burn API workflows in multiple programming languages.

### Table of Contents

* [Authentication](examples.md#authentication)
* [Mint Workflow](examples.md#mint-workflow)
* [Burn Workflow](examples.md#burn-workflow)
* [Order Tracking](examples.md#order-tracking)
* [Complete Integration](examples.md#complete-integration)

***

### Authentication

#### JavaScript/TypeScript

```typescript
const ISSUANCE_REDEMPTION_API_URL = 'https://api.universal.xyz/issuance-redemption';
const AUTH_API_URL = 'https://api.universal.xyz/auth';
const API_KEY = process.env.API_KEY; // Format: keyId.secret

interface TokenResponse {
  token: string;
  expiresAt: number;
}

async function issueToken(walletAddress: string): Promise<string> {
  const response = await fetch(`${AUTH_API_URL}/token`, {
    method: 'POST',
    headers: {
      'X-API-Key': API_KEY,
      'Content-Type': 'application/json',
    },
    body: JSON.stringify({ walletAddress }),
  });

  if (!response.ok) {
    throw new Error(`Failed to issue token: ${response.statusText}`);
  }

  const data: TokenResponse = await response.json();
  return data.token;
}

async function refreshToken(walletAddress: string): Promise<string> {
  const response = await fetch(`${AUTH_API_URL}/token/refresh`, {
    method: 'POST',
    headers: {
      'X-API-Key': API_KEY,
      'Content-Type': 'application/json',
    },
    body: JSON.stringify({ walletAddress }),
  });

  if (!response.ok) {
    throw new Error(`Failed to refresh token: ${response.statusText}`);
  }

  const data: TokenResponse = await response.json();
  return data.token;
}

// Usage
const walletAddress = '0x742d35Cc6634C0532925a3b8D4C9db96C4b4d8b1';
const token = await issueToken(walletAddress);
console.log('JWT Token:', token);
```

#### Python

```python
import os
import requests
from typing import Dict

ISSUANCE_REDEMPTION_API_URL = 'https://api.universal.xyz/issuance-redemption'
AUTH_API_URL = 'https://api.universal.xyz/auth'
API_KEY = os.getenv('API_KEY')  # Format: keyId.secret

def issue_token(wallet_address: str) -> str:
    """Issue a new JWT token for the specified wallet address."""
    response = requests.post(
        f'{AUTH_API_URL}/token',
        headers={
            'X-API-Key': API_KEY,
            'Content-Type': 'application/json',
        },
        json={'walletAddress': wallet_address}
    )

    response.raise_for_status()
    data = response.json()
    return data['token']

def refresh_token(wallet_address: str) -> str:
    """Refresh JWT token for the specified wallet address."""
    response = requests.post(
        f'{AUTH_API_URL}/token/refresh',
        headers={
            'X-API-Key': API_KEY,
            'Content-Type': 'application/json',
        },
        json={'walletAddress': wallet_address}
    )

    response.raise_for_status()
    data = response.json()
    return data['token']

# Usage
wallet_address = '0x742d35Cc6634C0532925a3b8D4C9db96C4b4d8b1'
token = issue_token(wallet_address)
print(f'JWT Token: {token}')
```

#### cURL

```bash
# Issue token
curl -X POST https://api.universal.xyz/auth/token \
  -H "X-API-Key: ${API_KEY}" \
  -H "Content-Type: application/json" \
  -d '{
    "walletAddress": "0x742d35Cc6634C0532925a3b8D4C9db96C4b4d8b1"
  }'

# Refresh token
curl -X POST https://api.universal.xyz/auth/token/refresh \
  -H "X-API-Key: ${API_KEY}" \
  -H "Content-Type: application/json" \
  -d '{
    "walletAddress": "0x742d35Cc6634C0532925a3b8D4C9db96C4b4d8b1"
  }'
```

***

### Mint Workflow

#### JavaScript/TypeScript

```typescript
interface MintWallet {
  address: string;
  chain: string;
  destinationChain: string;
}

async function createMintWallet(token: string, chain: string, destinationChain: string): Promise<MintWallet> {
  const response = await fetch(`${ISSUANCE_REDEMPTION_API_URL}/mint/wallets`, {
    method: 'POST',
    headers: {
      Authorization: `Bearer ${token}`,
      'Content-Type': 'application/json',
    },
    body: JSON.stringify({ chain, destinationChain }),
  });

  if (!response.ok) {
    const error = await response.json();
    throw new Error(error.message || 'Failed to create mint wallet');
  }

  const result = await response.json();
  return result.data;
}

async function getMintWallets(token: string): Promise<MintWallet[]> {
  const response = await fetch(`${ISSUANCE_REDEMPTION_API_URL}/mint/wallets`, {
    headers: {
      Authorization: `Bearer ${token}`,
    },
  });

  if (!response.ok) {
    throw new Error('Failed to fetch mint wallets');
  }

  const result = await response.json();
  return result.data;
}

// Complete mint workflow
async function mintWorkflow() {
  const walletAddress = '0x742d35Cc6634C0532925a3b8D4C9db96C4b4d8b1';

  // Step 1: Get JWT token
  const token = await issueToken(walletAddress);

  // Step 2: Check existing wallets
  const existingWallets = await getMintWallets(token);
  console.log('Existing wallets:', existingWallets);

  // Step 3: Create new deposit address (or use existing)
  const solToBase = existingWallets.find((w) => w.chain === 'sol' && w.destinationChain === 'base');

  let depositAddress: string;
  if (solToBase) {
    console.log('Using existing deposit address:', solToBase.address);
    depositAddress = solToBase.address;
  } else {
    const newWallet = await createMintWallet(token, 'sol', 'base');
    console.log('Created new deposit address:', newWallet.address);
    depositAddress = newWallet.address;
  }

  // Step 4: Instruct user to send SOL to deposit address
  console.log(`\nSend SOL to: ${depositAddress}`);
  console.log('Minimum: 0.01 SOL');
  console.log('The system will automatically mint uSOL to your wallet on Base.');

  return depositAddress;
}

mintWorkflow().catch(console.error);
```

#### Python

```python
from typing import List, Dict, Optional

def create_mint_wallet(token: str, chain: str, destination_chain: str) -> Dict:
    """Create a new mint deposit address."""
    response = requests.post(
        f'{ISSUANCE_REDEMPTION_API_URL}/mint/wallets',
        headers={
            'Authorization': f'Bearer {token}',
            'Content-Type': 'application/json',
        },
        json={
            'chain': chain,
            'destinationChain': destination_chain
        }
    )

    response.raise_for_status()
    result = response.json()
    return result['data']

def get_mint_wallets(token: str) -> List[Dict]:
    """Get all mint wallets for the authenticated user."""
    response = requests.get(
        f'{ISSUANCE_REDEMPTION_API_URL}/mint/wallets',
        headers={'Authorization': f'Bearer {token}'}
    )

    response.raise_for_status()
    result = response.json()
    return result['data']

# Complete mint workflow
def mint_workflow():
    wallet_address = '0x742d35Cc6634C0532925a3b8D4C9db96C4b4d8b1'

    # Step 1: Get JWT token
    token = issue_token(wallet_address)

    # Step 2: Check existing wallets
    existing_wallets = get_mint_wallets(token)
    print(f'Existing wallets: {existing_wallets}')

    # Step 3: Find or create SOL to Base wallet
    sol_to_base = next(
        (w for w in existing_wallets
         if w['chain'] == 'sol' and w['destinationChain'] == 'base'),
        None
    )

    if sol_to_base:
        print(f"Using existing deposit address: {sol_to_base['address']}")
        deposit_address = sol_to_base['address']
    else:
        new_wallet = create_mint_wallet(token, 'sol', 'base')
        print(f"Created new deposit address: {new_wallet['address']}")
        deposit_address = new_wallet['address']

    # Step 4: Instruct user
    print(f'\nSend SOL to: {deposit_address}')
    print('Minimum: 0.01 SOL')
    print('uSOL will be automatically minted to your wallet on Base.')

    return deposit_address

if __name__ == '__main__':
    mint_workflow()
```

***

### Burn Workflow

#### JavaScript/TypeScript

```typescript
interface BurnWallet {
  uAssetTransferAddress: string;
  destinationChain: string;
  destinationAddress: string;
  memo: string | null;
}

async function createBurnWallet(
  token: string,
  destinationChain: string,
  destinationAddress: string,
  memo?: string,
): Promise<BurnWallet> {
  const response = await fetch(`${ISSUANCE_REDEMPTION_API_URL}/burn/wallets`, {
    method: 'POST',
    headers: {
      Authorization: `Bearer ${token}`,
      'Content-Type': 'application/json',
    },
    body: JSON.stringify({
      destinationChain,
      destinationAddress,
      memo: memo || null,
    }),
  });

  if (!response.ok) {
    const error = await response.json();
    throw new Error(error.message || 'Failed to create burn wallet');
  }

  const result = await response.json();
  return result.data;
}

// Complete burn workflow
async function burnWorkflow() {
  const walletAddress = '0x742d35Cc6634C0532925a3b8D4C9db96C4b4d8b1';
  const solanaDestination = '9WzDXwBbmkg8ZTbNMqUxvQRAyrZzDsGYdLVL9zYtAWWM';

  // Step 1: Get JWT token
  const token = await issueToken(walletAddress);

  // Step 2: Create burn configuration
  const burnWallet = await createBurnWallet(token, 'sol', solanaDestination);
  console.log('Burn wallet created:', burnWallet);

  // Step 3: Instruct user to send uSOL
  const amount = '10000000000000000000'; // 10 uSOL in wei
  console.log(`\nTo burn uSOL and receive SOL (1:1 conversion, no fees):`);
  console.log(`1. Send ${amount} uSOL to: ${burnWallet.uAssetTransferAddress}`);
  console.log(`2. You will receive exactly 10 SOL at: ${solanaDestination}`);
  console.log(`3. Track status via Orders API`);

  return burnWallet;
}

burnWorkflow().catch(console.error);
```

#### Python

```python
def create_burn_wallet(
    token: str,
    destination_chain: str,
    destination_address: str,
    memo: Optional[str] = None
) -> Dict:
    """Create a burn wallet configuration."""
    response = requests.post(
        f'{ISSUANCE_REDEMPTION_API_URL}/burn/wallets',
        headers={
            'Authorization': f'Bearer {token}',
            'Content-Type': 'application/json',
        },
        json={
            'destinationChain': destination_chain,
            'destinationAddress': destination_address,
            'memo': memo
        }
    )

    response.raise_for_status()
    result = response.json()
    return result['data']

# Complete burn workflow
def burn_workflow():
    wallet_address = '0x742d35Cc6634C0532925a3b8D4C9db96C4b4d8b1'
    solana_destination = '9WzDXwBbmkg8ZTbNMqUxvQRAyrZzDsGYdLVL9zYtAWWM'

    # Step 1: Get JWT token
    token = issue_token(wallet_address)

    # Step 2: Create burn configuration
    burn_wallet = create_burn_wallet(token, 'sol', solana_destination)
    print(f'Burn wallet created: {burn_wallet}')

    # Step 3: Instruct user
    amount = '10000000000000000000'  # 10 uSOL in wei
    print(f"\nTo burn uSOL and receive SOL (1:1 conversion, no fees):")
    print(f"1. Send {amount} uSOL to: {burn_wallet['uAssetTransferAddress']}")
    print(f"2. You will receive exactly 10 SOL at: {solana_destination}")
    print(f"3. Track status via Orders API")

    return burn_wallet

if __name__ == '__main__':
    burn_workflow()
```

#### XRP Burn Example (with Memo)

```typescript
async function burnToXRP() {
  const walletAddress = '0x742d35Cc6634C0532925a3b8D4C9db96C4b4d8b1';
  const xrpDestination = 'rN7n7otQDd6FczFgLdlqtyMVUXWqzp1GE1';
  const xrpMemo = '2740406962'; // Numeric destination tag

  const token = await issueToken(walletAddress);

  // Create burn configuration with memo
  const burnWallet = await createBurnWallet(token, 'xrp', xrpDestination, xrpMemo);

  console.log('XRP Burn Configuration:');
  console.log(`Transfer uXRP to: ${burnWallet.uAssetTransferAddress}`);
  console.log(`XRP will be sent to: ${xrpDestination}`);
  console.log(`Destination Tag: ${xrpMemo}`);
  console.log(`Minimum: 25 uXRP (1:1 conversion - you receive exactly what you burn)`);

  return burnWallet;
}
```

***

### Order Tracking

#### JavaScript/TypeScript

```typescript
interface Order {
  id: string;
  type: 'mint' | 'burn';
  status: string;
  sourceChain: string;
  destinationChain: string;
  token: string;
  amount: string;
  sourceWallet: string;
  destinationWallet: string;
  workflowId: string;
  transactionHash: string | null;
  errorMessage: string | null;
  createdAt: string;
  updatedAt: string | null;
}

async function getOrders(
  token: string,
  options?: {
    limit?: number;
    offset?: number;
    type?: 'mint' | 'burn';
    status?: string;
  },
): Promise<{ orders: Order[]; pagination: any }> {
  const params = new URLSearchParams();
  if (options?.limit) params.set('limit', options.limit.toString());
  if (options?.offset) params.set('offset', options.offset.toString());
  if (options?.type) params.set('type', options.type);
  if (options?.status) params.set('status', options.status);

  const response = await fetch(`${ISSUANCE_REDEMPTION_API_URL}/orders?${params}`, {
    headers: { Authorization: `Bearer ${token}` },
  });

  if (!response.ok) {
    throw new Error('Failed to fetch orders');
  }

  const result = await response.json();
  return result.data;
}

async function waitForOrderCompletion(token: string, orderId: string, pollInterval = 10000): Promise<Order> {
  while (true) {
    const { orders } = await getOrders(token, { limit: 100 });
    const order = orders.find((o) => o.id === orderId);

    if (!order) {
      throw new Error(`Order ${orderId} not found`);
    }

    console.log(`Order ${orderId} status: ${order.status}`);

    if (order.status === 'completed') {
      console.log(`Order completed! Transaction: ${order.transactionHash}`);
      return order;
    }

    if (order.status === 'failed') {
      throw new Error(`Order failed: ${order.errorMessage}`);
    }

    // Poll again after interval
    await new Promise((resolve) => setTimeout(resolve, pollInterval));
  }
}

// Usage
async function trackOrders() {
  const walletAddress = '0x742d35Cc6634C0532925a3b8D4C9db96C4b4d8b1';
  const token = await issueToken(walletAddress);

  // Get all orders
  const { orders, pagination } = await getOrders(token);
  console.log(`Total orders: ${pagination.total}`);
  console.log(`Showing ${orders.length} orders`);

  // Get only completed mint orders
  const completedMints = await getOrders(token, {
    type: 'mint',
    status: 'completed',
    limit: 50,
  });
  console.log(`Completed mints: ${completedMints.orders.length}`);

  // Wait for specific order
  if (orders.length > 0 && orders[0].status !== 'completed') {
    await waitForOrderCompletion(token, orders[0].id);
  }
}

trackOrders().catch(console.error);
```

***

### Complete Integration

#### Full Mint and Burn Integration

```typescript
class UniversalBridgeClient {
  private apiKey: string;
  private bridgeUrl: string;
  private authUrl: string;
  private tokenCache: Map<string, { token: string; expiresAt: number }>;

  constructor(
    apiKey: string,
    bridgeUrl = 'https://api.universal.xyz/bridge',
    authUrl = 'https://api.universal.xyz/auth',
  ) {
    this.apiKey = apiKey;
    this.bridgeUrl = bridgeUrl;
    this.authUrl = authUrl;
    this.tokenCache = new Map();
  }

  async getToken(walletAddress: string): Promise<string> {
    const cached = this.tokenCache.get(walletAddress);
    const now = Math.floor(Date.now() / 1000);

    // Return cached token if valid for at least 1 more hour
    if (cached && cached.expiresAt > now + 3600) {
      return cached.token;
    }

    // Issue or refresh token
    const response = await fetch(`${this.authUrl}/token`, {
      method: 'POST',
      headers: {
        'X-API-Key': this.apiKey,
        'Content-Type': 'application/json',
      },
      body: JSON.stringify({ walletAddress }),
    });

    if (!response.ok) {
      throw new Error(`Token issuance failed: ${response.statusText}`);
    }

    const data = await response.json();
    this.tokenCache.set(walletAddress, data);

    return data.token;
  }

  async createMintWallet(walletAddress: string, chain: string, destinationChain: string): Promise<MintWallet> {
    const token = await this.getToken(walletAddress);

    const response = await fetch(`${this.bridgeUrl}/mint/wallets`, {
      method: 'POST',
      headers: {
        Authorization: `Bearer ${token}`,
        'Content-Type': 'application/json',
      },
      body: JSON.stringify({ chain, destinationChain }),
    });

    if (!response.ok) {
      const error = await response.json();
      throw new Error(error.message);
    }

    const result = await response.json();
    return result.data;
  }

  async createBurnWallet(
    walletAddress: string,
    destinationChain: string,
    destinationAddress: string,
    memo?: string,
  ): Promise<BurnWallet> {
    const token = await this.getToken(walletAddress);

    const response = await fetch(`${this.bridgeUrl}/burn/wallets`, {
      method: 'POST',
      headers: {
        Authorization: `Bearer ${token}`,
        'Content-Type': 'application/json',
      },
      body: JSON.stringify({
        destinationChain,
        destinationAddress,
        memo: memo || null,
      }),
    });

    if (!response.ok) {
      const error = await response.json();
      throw new Error(error.message);
    }

    const result = await response.json();
    return result.data;
  }

  async getOrders(walletAddress: string, options?: { type?: 'mint' | 'burn'; status?: string }): Promise<Order[]> {
    const token = await this.getToken(walletAddress);
    const params = new URLSearchParams();

    if (options?.type) params.set('type', options.type);
    if (options?.status) params.set('status', options.status);

    const response = await fetch(`${this.bridgeUrl}/orders?${params}`, {
      headers: { Authorization: `Bearer ${token}` },
    });

    if (!response.ok) {
      throw new Error('Failed to fetch orders');
    }

    const result = await response.json();
    return result.data.orders;
  }
}

// Usage example
async function main() {
  const client = new UniversalBridgeClient(process.env.API_KEY!);
  const walletAddress = '0x742d35Cc6634C0532925a3b8D4C9db96C4b4d8b1';

  // Mint workflow
  console.log('=== MINT WORKFLOW ===');
  const mintWallet = await client.createMintWallet(walletAddress, 'sol', 'base');
  console.log(`Deposit SOL to: ${mintWallet.address}`);

  // Burn workflow
  console.log('\n=== BURN WORKFLOW ===');
  const burnWallet = await client.createBurnWallet(
    walletAddress,
    'sol',
    '9WzDXwBbmkg8ZTbNMqUxvQRAyrZzDsGYdLVL9zYtAWWM',
  );
  console.log(`Send uSOL to: ${burnWallet.uAssetTransferAddress}`);

  // Track orders
  console.log('\n=== ORDERS ===');
  const orders = await client.getOrders(walletAddress);
  console.log(`Total orders: ${orders.length}`);

  orders.forEach((order) => {
    console.log(`${order.type} | ${order.status} | ${order.token} | ${order.amount}`);
  });
}

main().catch(console.error);
```

### Next Steps

* [Authentication](authentication.md) - Deep dive into auth mechanisms
* [Mint API](mint-api.md) - Complete mint API reference
* [Burn API](burn-api.md) - Complete burn API reference
* [Error Handling ](error-handling.md)- Handle errors gracefully
