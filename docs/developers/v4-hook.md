# Uniswap V4 Hook

**The most capital-efficient way to enable trading of wrapped assets on your app.**

## Introduction

Universal is a wrapped asset protocol designed to bring any token to any chain. While our [Universal API](api.md) has successfully powered millions in volume via a Request-for-Quote (RFQ) model—perfect for wallets and apps requiring guaranteed pricing—high-frequency trading and onchain composability demand zero latency.

The **Universal v4 Hook** is the next frontier: a Uniswap V4 integration that enables synchronous, Just-In-Time (JIT) liquidity for seamless spot trading, directly through Uniswap.

---

## Why Integrate the V4 Hook?

### Zero Custom Integration
The Hook exposes a standard Uniswap V4 pool interface. If you already support Uniswap V4, you can route to Universal pools with no additional code.

### Atomic Execution
Single-transaction swaps. No offchain quote requests, no signatures, no waiting for settlement—just a standard swap call.

### Always-On Liquidity
Deep liquidity is always available within oracle-defined bands. No need to check if a pool has sufficient TVL or handle low-liquidity edge cases.

### Instant Composability
Works out-of-the-box with your existing infrastructure:
- DEX aggregator routing
- Multi-hop swaps
- Smart contract integrations

---

## Supported Assets & Chains

| Assets | Chains |
|--------|--------|
| uTAO, uXRP, uZEC, uDOGE, uHYPE | Unichain |

*More assets and chains rolling out soon.*

---

## RFQ vs V4 Hook: When to Use What

| Feature | Universal API (RFQ) | Universal v4 Hook |
|---------|---------------------|-------------------|
| **Model** | Asynchronous Request-for-Quote | Synchronous / Atomic Swap |
| **Liquidity** | Merchant-quoted on demand | JIT Oracle-based Liquidity Bands |
| **Execution** | Multi-step (Sign → Wait → Settle) | Single Transaction |
| **Best For** | Platform-wide integrations, wallets | DEX Aggregators, arbitrageurs, trading apps |

---

## How It Works

The v4 Hook eliminates the need for pre-funded LP positions. Instead of idle capital sitting in a pool:

1. **Oracle-Driven Bands:** An authorized oracle continuously updates liquidity bands (price ticks + max volume) for each trading direction
2. **JIT Execution:** When a user swaps, the Hook intercepts the transaction via `beforeSwap`
3. **On-Demand Settlement:** The Hook dynamically mints uAssets or settles USDC from the merchant's balance—all within the same transaction
4. **Virtual Pricing:** A virtual price state tracks execution within the bands to prevent sandwich attacks

---

## Background: Traditional RFQ System

The existing Universal API system operates using a Request for Quote (RFQ) model:

1. **User Request**: A user requests a quote for swapping between a wrapped asset and USDC
2. **Merchant Quote**: A merchant calculates the cost to buy the underlying asset and returns a quote
3. **User Signature**: If acceptable, the user signs the quote
4. **Merchant Execution**: The merchant:
    - Purchases the underlying asset
    - Sends it to custody
    - Waits for attestation service validation
5. **Settlement**: Once both user signature and attestation are received, the merchant mints the wrapped asset and settles with the user

**Limitations**: This process is asynchronous, requires multiple steps, and introduces latency between quote and execution.

## New Solution: JIT Oracle + Uniswap V4 Hook

The new system eliminates the RFQ flow by providing continuous, oracle-based liquidity through Uniswap V4.

### Architecture Components

### 1. UniversalJITOracle (`src/v4-hook/UniversalJITOracle.sol`)

A specialized oracle that provides real-time liquidity bands for each trading direction.

**Key Features:**

- **Dual Liquidity Bands**: Maintains separate bands for each swap direction
    - Lower band: For selling USDC → buying uAsset (zeroForOne swaps)
    - Upper band: For selling uAsset → buying USDC (oneForZero swaps)
- **Oracle Updates**: Authorized reporters continuously update price and liquidity data
    - `jitLowerTick` / `jitUpperTick`: Start prices in ticks for the price curve for each direction
    - `jitLowerRange` / `jitUpperRange`: Tick ranges defining band width
    - `uAssetLiquidity`: Maximum uAsset liquidity available
    - `usdcLiquidity`: Maximum USDC liquidity available
    - `timestamp`: Update timestamp, saved automatically and used to check for stale prices
- **Safety Mechanisms**:
    - Price staleness checks (`STALE_THRESHOLD`: 30 seconds)
    - Update frequency limits (`MIN_FREQUENCY`: 5 seconds)
    - Maximum price delta protection (`maxDeltaTick`)
    - Role-based access control for oracle reporters

**Data Structure:**

```solidity
struct OracleUpdate {
    PoolId poolId;           // Uniswap V4 pool identifier
    int24 jitLowerTick;      // Center tick for lower band
    int24 jitUpperTick;      // Center tick for upper band
    uint16 jitLowerRange;    // Width of lower band
    uint16 jitUpperRange;    // Width of upper band
    uint72 uAssetLiquidity;  // Max uAsset liquidity (scaled by 1e12)
    uint72 usdcLiquidity;    // Max USDC liquidity
}
```

### 2. UniversalJITHook (`src/v4-hook/UniversalJitHook.sol`)

A Uniswap V4 hook that intercepts swaps and executes them against the oracle-defined liquidity bands.

**Key Features:**

- **beforeSwap Hook**: Intercepts all swaps before they hit the pool
- **Virtual Price Tracking**: Maintains virtual price state for each direction in between oracle updates to prevent price manipulation
- **Dynamic Settlement**:
    - Uses existing pool claims when available
    - Mints new uAssets on-demand via MerchantController
    - Settles USDC from merchant balance
- **Blacklist Enforcement**: Checks user blacklist status via wrapped asset contracts

**Hook Permissions:**

```solidity
beforeInitialize: true       // Validate pool setup
beforeSwap: true            // Intercept swaps
beforeSwapReturnDelta: true // Return custom swap results
beforeAddLiquidity: true    // Block direct liquidity provision
beforeRemoveLiquidity: true // Block direct liquidity removal
beforeDonate: true          // Block donations
```

### Trading Flow

### Zero-for-One Swap (Selling Token0 → Buying Token1)

**Example: USDC → uAsset**

1. **Oracle Query**: Hook fetches latest oracle report for the pool
2. **Band Calculation**: Computes lower band range
    - `tickLower = jitLowerTick - jitLowerRange`
    - `tickUpper = jitLowerTick`
    - `sqrtPriceLower = TickMath.getSqrtPriceAtTick(tickLower)`
    - `sqrtPriceUpper = virtualSqrtPriceX96` (tracked state)
3. **Liquidity Calculation**: Derives liquidity from oracle
    - If buying uAsset: `liquidity = getLiquidityForAmount1(sqrtPriceLower, sqrtPriceUpper, uAssetLiquidity)`
    - If buying USDC: `liquidity = getLiquidityForAmount1(sqrtPriceLower, sqrtPriceUpper, usdcLiquidity)`
4. **Quote Calculation**: Uses constant product formula within virtual price range
    - Exact Input: Calculate output given specified input
    - Exact Output: Calculate input needed for specified output
    - Respects `sqrtPriceLimitX96` from swap parameters
5. **Virtual Price Update**: Updates virtual price for the direction
    - `lowerVirtualPrice[poolId].updatePrice(sqrtPriceAfter)`
6. **Settlement**:
    - **Take**: Collect input tokens from swapper via PoolManager
    - **Settle**: Provide output tokens to swapper
        - Use existing pool claims first
        - Mint new uAssets if needed (via `merchantController.mintFromHook`)
        - Use merchant USDC balance for USDC swaps
7. **Return Delta**: Hook returns `BeforeSwapDelta` to PoolManager
    - Pool execution is bypassed (no-op)
    - All liquidity provided by hook

### One-for-Zero Swap (Selling Token1 → Buying Token0)

**Example: uAsset → USDC**

Similar flow but uses upper band:

- `tickLower = jitUpperTick`
- `tickUpper = jitUpperTick + jitUpperRange`
- Uses `upperVirtualPrice[poolId]` for state tracking

### Security Features

### Pool Initialization Checks

When a new pool is initialized with the hook:

1. **Currency Validation**: One currency must be PAIR_TOKEN (USDC), the other must be whitelisted in MerchantController
2. **Oracle Availability**: Oracle must have a valid, non-stale price report for the pool

### Per-Swap Checks

Before each swap:

1. **Asset Whitelist**: Wrapped asset must be whitelisted in MerchantController
2. **User Blacklist**: Transaction originator must not be blacklisted on the wrapped asset contract
3. **Oracle Freshness**: Oracle report must be less than 30 seconds old
4. **Liquidity Availability**:
    - uAsset minting must be available via MerchantController
    - Merchant must have sufficient USDC balance for USDC settlements

### Price Manipulation Protection

1. **Virtual Price State**: Each direction maintains a virtual price that only moves within oracle-defined bands
2. **Oracle Update Limits**:
    - Minimum 5 seconds between updates
    - Maximum tick delta (`maxDeltaTick`) enforced between consecutive updates
3. **Staleness Checks**: Prices older than 30 seconds are rejected

### Post-Swap Cleanup

### Sweep Mechanism (`sweepToMerchant`)

After swaps, residual claims may remain in the hook contract. The sweep function:

1. Burns uAsset claims (takes tokens and burns via MerchantController)
2. Transfers USDC claims to merchant
3. Can be called by anyone for any valid pool
4. Ensures the hook doesn't accumulate residual balances

### Integration Points

### MerchantController Interface

```solidity
interface IMinimalMerchantController {
    function isAssetWhitelisted(address asset) external view returns (bool);
    function mintFromHook(address asset, address to, uint256 amount) external;
    function burnFromHook(address asset, address from, uint256 amount) external;
}
```

### Oracle Interface

```solidity
interface IMinimalJITOracle {
    struct OracleReport {
        int24 jitLowerTick;
        int24 jitUpperTick;
        uint24 jitLowerRange;
        uint24 jitUpperRange;
        uint256 uAssetLiquidity;
        uint256 usdcLiquidity;
        uint48 timestamp;
    }

    function safeGetPrice(PoolId poolId) external view returns (OracleReport memory);
}
```

### Key Advantages

1. **Instant Execution**: No waiting for quotes or attestations
2. **Continuous Liquidity**: Always-on liquidity within oracle-defined bands
3. **Price Control**: Merchant controls exact pricing through oracle updates
4. **Capital Efficiency**: On-demand minting eliminates need for pre-minted inventory
5. **Gas Efficiency**: Single transaction for complete swap
6. **MEV Protection**: Virtual price state prevents sandwich attacks within band

### Technical Considerations

### Liquidity Scaling

The oracle stores `uAssetLiquidity` as `uint72` scaled down by `UASSET_SCALE` (1e12) to fit within storage constraints:

```solidity
// Oracle storage (scaled down)
uint72 uAssetLiquidity;

// Hook reads and scales up
function getPrice(PoolId poolId) public view returns (OracleReport memory) {
    return OracleReport({
        uAssetLiquidity: uint256(oracleReports[poolId].uAssetLiquidity) * UASSET_SCALE,
        // ... other fields
    });
}
```

### Virtual Price Mechanics

Virtual prices track the effective execution price within bands:

```solidity
struct VirtualPrice {
    uint160 virtualSqrtPriceX96;
    uint32 timestamp;
}
```

- Updated only when oracle timestamp is newer
- Moves with each swap within the band
- Resets to oracle price when oracle updates

### Claims-Based Settlement

The hook uses Uniswap V4's claims system for gas efficiency:

1. **Taking Tokens**: Hook receives claims (IOU from PoolManager)
2. **Settling Tokens**: Hook burns claims and provides tokens
3. **Claim Cleanup**: Unused claims are burned and underlying tokens handled appropriately

### Example Scenario

**User wants to buy 1000 uBTC with USDC:**

1. Oracle reports:
    - `jitLowerTick`: -100
    - `jitLowerRange`: 50
    - `uAssetLiquidity`: 10,000,000,000,000,000 (10 BTC worth, scaled)
    - `usdcLiquidity`: 800,000,000,000 (800k USDC)
2. Hook calculates:
    - Band range: ticks -150 to -100
    - Liquidity: Based on 10 BTC available
    - Virtual price: Current state within band
3. User swaps:
    - Hook computes: 1000 uBTC requires X USDC
    - Takes X USDC from user
    - Checks claims, mints remaining 1000 uBTC via MerchantController
    - Settles 1000 uBTC to user
4. Virtual price updates to reflect execution
5. Later, someone calls `sweepToMerchant()` to clean up any residual claims

## Configuration

### Oracle Setup

```solidity
// Deploy oracle
UniversalJITOracle oracle = new UniversalJITOracle(maxDeltaTick);

// Grant reporter role
oracle.grantRole(ORACLE_REPORTER_ROLE, reporterAddress);

// Reporter updates prices
OracleUpdate memory update = OracleUpdate({
    poolId: poolId,
    jitLowerTick: -100,
    jitUpperTick: 100,
    jitLowerRange: 50,
    jitUpperRange: 50,
    uAssetLiquidity: 10_000_000_000_000_000,
    usdcLiquidity: 1_000_000_000_000
});
oracle.setPrice(update);
```

### Hook Setup

```solidity
// Deploy hook
UniversalJITHook hook = new UniversalJITHook(
    poolManager,
    oracle,
    merchantController,
    pairToken,  // USDC
    owner,
    merchant
);

// Initialize pool with hook
PoolKey memory key = PoolKey({
    currency0: usdc,
    currency1: uAsset,
    fee: 0,
    tickSpacing: 1,
    hooks: IHooks(address(hook))
});
poolManager.initialize(key, initialSqrtPrice, "");
```

## Future Enhancements

Potential improvements to consider:

1. **Native swap logic execution:** use Uniswap's native swap mechanic by adding/removing liquidity JIT. As this comes with it's unique set of challenges, like respecting tick spacing while ensuring liquidity is single sided, sqrtPrice jumping when there's no third party liquidity and oracle liquidity is depleted or price is stale, etc.
2. **Multi-Merchant Support**: Allow multiple liquidity providers
3. **Fee Mechanisms**: Introduce configurable fees for the protocol

---

## Integration

Integrating the v4 Hook is as simple as interacting with any standard Uniswap V4 pool.

**For Aggregators & Traders:**
You do not need custom ABI methods. Simply route trades through the pool ID associated with the Universal Hook—the smart contract handles all the complexity of JIT minting and settlement.

**Contract Address (Unichain):**
```
0xcdfCaB084b2d29025772141d3BF473bd9673aaA8
```

---

## Get Started

- **Integration Support:** [dev@universal.xyz](mailto:dev@universal.xyz)
- **Smart Contracts:** [Contract Addresses](smart-contracts.md)
- **API Documentation:** [Universal API](api.md)
