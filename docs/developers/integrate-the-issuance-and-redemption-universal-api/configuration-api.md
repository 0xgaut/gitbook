# Configuration API

### Overview

The Configuration API provides information about supported chains, tokens, and network configurations. Use these endpoints to dynamically discover available mint and burn options.

### Endpoints

#### Get Mint Configuration

Retrieves supported source chains, destination chains, and tokens for mint operations.

**Endpoint**: `GET /config/mint`

**Headers**: None required (public endpoint)

**Response** (200 OK):

```json
{
  "success": true,
  "data": {
    "sourceChains": [
      {
        "name": "Solana",
        "symbol": "sol",
        "type": "main",
        "tokens": [
          {
            "name": "Solana",
            "symbol": "SOL",
            "decimals": 9
          }
        ]
      },
      {
        "name": "XRP Ledger",
        "symbol": "xrp",
        "type": "main",
        "tokens": [
          {
            "name": "XRP",
            "symbol": "XRP",
            "decimals": 6
          }
        ]
      },
      {
        "name": "Sui",
        "symbol": "sui",
        "type": "main",
        "tokens": [
          {
            "name": "Sui",
            "symbol": "SUI",
            "decimals": 9
          }
        ]
      },
      {
        "name": "Dogecoin",
        "symbol": "doge",
        "type": "main",
        "tokens": [
          {
            "name": "Dogecoin",
            "symbol": "DOGE",
            "decimals": 8
          }
        ]
      },
      {
        "name": "Zcash",
        "symbol": "zec",
        "type": "main",
        "tokens": [
          {
            "name": "Zcash",
            "symbol": "ZEC",
            "decimals": 8
          }
        ]
      }
    ],
    "destinationChains": [
      {
        "name": "Base",
        "symbol": "base",
        "type": "main",
        "chainId": 8453,
        "vm": "EVM",
        "tokens": [
          {
            "name": "Universal Solana",
            "symbol": "uSOL",
            "decimals": 18
          },
          {
            "name": "Universal XRP",
            "symbol": "uXRP",
            "decimals": 18
          },
          {
            "name": "Universal Sui",
            "symbol": "uSUI",
            "decimals": 18
          },
          {
            "name": "Universal Dogecoin",
            "symbol": "uDOGE",
            "decimals": 18
          },
          {
            "name": "Universal Zcash",
            "symbol": "uZEC",
            "decimals": 18
          }
        ]
      },
      {
        "name": "Arbitrum",
        "symbol": "arbitrum",
        "type": "main",
        "chainId": 42161,
        "vm": "EVM",
        "tokens": [
          {
            "name": "Universal Solana",
            "symbol": "uSOL",
            "decimals": 18
          },
          {
            "name": "Universal XRP",
            "symbol": "uXRP",
            "decimals": 18
          },
          {
            "name": "Universal Sui",
            "symbol": "uSUI",
            "decimals": 18
          },
          {
            "name": "Universal Dogecoin",
            "symbol": "uDOGE",
            "decimals": 18
          },
          {
            "name": "Universal Zcash",
            "symbol": "uZEC",
            "decimals": 18
          }
        ]
      }
    ],
    "supportedTokens": [
      {
        "name": "Solana",
        "symbol": "SOL",
        "decimals": 9
      },
      {
        "name": "XRP",
        "symbol": "XRP",
        "decimals": 6
      },
      {
        "name": "Sui",
        "symbol": "SUI",
        "decimals": 9
      },
      {
        "name": "Dogecoin",
        "symbol": "DOGE",
        "decimals": 8
      },
      {
        "name": "Zcash",
        "symbol": "ZEC",
        "decimals": 8
      }
    ]
  }
}
```

**cURL Example**:

```bash
curl -X GET https://api.universal.xyz/issuance-redemption/config/mint
```

#### Get Burn Configuration

Retrieves supported source chains, destination chains, and tokens for burn operations.

**Endpoint**: `GET /config/burn`

**Headers**: None required (public endpoint)

**Response** (200 OK):

```json
{
  "success": true,
  "data": {
    "sourceChains": [
      {
        "name": "Base",
        "symbol": "base",
        "type": "main",
        "chainId": 8453,
        "vm": "EVM",
        "tokens": [
          {
            "name": "Universal Solana",
            "symbol": "uSOL",
            "decimals": 18
          },
          {
            "name": "Universal XRP",
            "symbol": "uXRP",
            "decimals": 18
          },
          {
            "name": "Universal Sui",
            "symbol": "uSUI",
            "decimals": 18
          },
          {
            "name": "Universal Dogecoin",
            "symbol": "uDOGE",
            "decimals": 18
          },
          {
            "name": "Universal Zcash",
            "symbol": "uZEC",
            "decimals": 18
          }
        ]
      },
      {
        "name": "Arbitrum",
        "symbol": "arbitrum",
        "type": "main",
        "chainId": 42161,
        "vm": "EVM",
        "tokens": [
          {
            "name": "Universal Solana",
            "symbol": "uSOL",
            "decimals": 18
          },
          {
            "name": "Universal XRP",
            "symbol": "uXRP",
            "decimals": 18
          },
          {
            "name": "Universal Sui",
            "symbol": "uSUI",
            "decimals": 18
          },
          {
            "name": "Universal Dogecoin",
            "symbol": "uDOGE",
            "decimals": 18
          },
          {
            "name": "Universal Zcash",
            "symbol": "uZEC",
            "decimals": 18
          }
        ]
      }
    ],
    "destinationChains": [
      {
        "name": "Solana",
        "symbol": "sol",
        "type": "main",
        "tokens": [
          {
            "name": "Solana",
            "symbol": "SOL",
            "decimals": 9
          }
        ]
      },
      {
        "name": "XRP Ledger",
        "symbol": "xrp",
        "type": "main",
        "tokens": [
          {
            "name": "XRP",
            "symbol": "XRP",
            "decimals": 6
          }
        ]
      },
      {
        "name": "Sui",
        "symbol": "sui",
        "type": "main",
        "tokens": [
          {
            "name": "Sui",
            "symbol": "SUI",
            "decimals": 9
          }
        ]
      },
      {
        "name": "Dogecoin",
        "symbol": "doge",
        "type": "main",
        "tokens": [
          {
            "name": "Dogecoin",
            "symbol": "DOGE",
            "decimals": 8
          }
        ]
      },
      {
        "name": "Zcash",
        "symbol": "zec",
        "type": "main",
        "tokens": [
          {
            "name": "Zcash",
            "symbol": "ZEC",
            "decimals": 8
          }
        ]
      }
    ],
    "supportedTokens": [
      {
        "symbol": "uSOL",
        "name": "Universal Solana",
        "decimals": 18,
        "minimumBurnAmount": "1000000000000000000"
      },
      {
        "symbol": "uXRP",
        "name": "Universal XRP",
        "decimals": 18,
        "minimumBurnAmount": "25000000000000000000"
      },
      {
        "symbol": "uSUI",
        "name": "Universal Sui",
        "decimals": 18,
        "minimumBurnAmount": "1000000000000000000"
      },
      {
        "symbol": "uDOGE",
        "name": "Universal Dogecoin",
        "decimals": 18,
        "minimumBurnAmount": "100000000000000000"
      },
      {
        "symbol": "uZEC",
        "name": "Universal Zcash",
        "decimals": 18,
        "minimumBurnAmount": "100000000000000000"
      }
    ]
  }
}
```

**cURL Example**:

```bash
curl -X GET https://api.universal.xyz/issuance-redemption/config/burn
```

### Response Fields

#### Chain Object

| Field     | Type   | Description                                  |
| --------- | ------ | -------------------------------------------- |
| `name`    | string | Full chain name (e.g., "Base", "Solana")     |
| `symbol`  | string | Short chain identifier (e.g., "base", "sol") |
| `type`    | string | Network type - `main` for mainnet            |
| `chainId` | number | EVM chain ID (EVM chains only)               |
| `vm`      | string | Virtual machine type - `EVM` for EVM chains  |
| `tokens`  | array  | Array of supported tokens on this chain      |

#### Token Object

| Field               | Type   | Description                                          |
| ------------------- | ------ | ---------------------------------------------------- |
| `name`              | string | Full token name (e.g., "Solana", "Universal Solana") |
| `symbol`            | string | Token symbol (e.g., "SOL", "uSOL")                   |
| `decimals`          | number | Number of decimal places                             |
| `minimumBurnAmount` | string | Minimum burn amount in wei (burn config only)        |

### Supported Networks

**Source Chains** (Mint):

* `sol` (Solana)
* `xrp` (XRP Ledger)
* `sui` (Sui)
* `doge` (Dogecoin)
* `zec` (Zcash)

**Destination Chains** (Mint/Burn):

* `base` (Base - Chain ID: 8453)
* `arbitrum` (Arbitrum One - Chain ID: 42161)
* `katana` (Katana - Chain ID: 747474)
* `world` (World Chain - Chain ID: 480)
* `unichain` (Unichain - Chain ID: 130)

### Use Cases

#### Dynamic UI Generation

```javascript
async function buildChainSelector() {
  const response = await fetch('https://api.universal.xyz/issuance-redemption/config/mint');
  const config = await response.json();

  // Populate source chain dropdown
  const sourceChains = config.data.sourceChains.map((chain) => ({
    value: chain.symbol,
    label: chain.name,
    tokens: chain.tokens,
  }));

  // Populate destination chain dropdown
  const destinationChains = config.data.destinationChains.map((chain) => ({
    value: chain.symbol,
    label: `${chain.name} (Chain ID: ${chain.chainId})`,
    tokens: chain.tokens,
  }));

  return { sourceChains, destinationChains };
}
```

#### Validate User Input

```javascript
async function validateMintRequest(sourceChain, destinationChain) {
  const response = await fetch('https://api.universal.xyz/issuance-redemption/config/mint');
  const config = await response.json();

  const validSourceChain = config.data.sourceChains.some(
    (chain) => chain.symbol === sourceChain && chain.type === 'main',
  );

  const validDestinationChain = config.data.destinationChains.some(
    (chain) => chain.symbol === destinationChain && chain.type === 'main',
  );

  return validSourceChain && validDestinationChain;
}
```

#### Get Token Decimals

```javascript
async function getTokenDecimals(tokenSymbol, operation = 'mint') {
  const endpoint = operation === 'mint' ? '/config/mint' : '/config/burn';
  const response = await fetch(`https://api.universal.xyz/issuance-redemption${endpoint}`);
  const config = await response.json();

  const token = config.data.supportedTokens.find((t) => t.symbol === tokenSymbol);
  return token?.decimals;
}

// Example usage
const solDecimals = await getTokenDecimals('SOL', 'mint'); // 9
const usolDecimals = await getTokenDecimals('uSOL', 'burn'); // 18
```

#### Check Minimum Burn Amount

```javascript
async function getMinimumBurnAmount(uAssetSymbol) {
  const response = await fetch('https://api.universal.xyz/issuance-redemption/config/burn');
  const config = await response.json();

  const token = config.data.supportedTokens.find((t) => t.symbol === uAssetSymbol);
  return token?.minimumBurnAmount;
}

// Example usage
const minUSOL = await getMinimumBurnAmount('uSOL'); // "1000000000000000000" (1 uSOL)
```

### Chain IDs Reference

#### EVM Chains

| Chain        | Chain ID | Symbol   |
| ------------ | -------- | -------- |
| Base         | 8453     | base     |
| Arbitrum One | 42161    | arbitrum |
| Katana       | 747474   | katana   |
| World Chain  | 480      | world    |
| Unichain     | 130      | unichain |

### Token Decimals Reference

#### Native Tokens (Mint Source)

| Token | Decimals | Smallest Unit     |
| ----- | -------- | ----------------- |
| SOL   | 9        | lamports          |
| XRP   | 6        | drops             |
| SUI   | 9        | MIST              |
| DOGE  | 8        | koinus (satoshis) |
| ZEC   | 8        | zatoshis          |

#### Universal Tokens (All uAssets)

| Token                         | Decimals | Smallest Unit |
| ----------------------------- | -------- | ------------- |
| uSOL, uXRP, uSUI, uDOGE, uZEC | 18       | wei           |

> **Note**: All universal tokens (uAssets) use 18 decimals, regardless of their native token's decimals.

### Best Practices

1. **Cache Configuration**: Cache config responses for 1-24 hours to reduce API calls
2. **Filter by Type**: In production, always filter chains by `type: "main"`
3. **Dynamic Validation**: Use config endpoints to validate user input dynamically
4. **Decimal Handling**: Use the provided decimals for accurate amount conversion
5. **Refresh Periodically**: Refresh configuration periodically to catch new chain additions

### Next Steps

* [Mint API](mint-api.md) - Use chain configuration for mint operations
* [Burn API](burn-api.md) - Use token minimums for burn operations
* [Examples ](examples.md)- See configuration in action
