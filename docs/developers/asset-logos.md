# Asset Logos

Access official uAsset token logos for use in your application.

## Logo URL Format

Easily access official **uAsset token logos** by using the following URL format:

```
https://www.universal.xyz/wrapped-tokens/UA-TOKEN.svg
```

Simply replace **`TOKEN`** with the desired asset's symbol.

## Examples

### Bitcoin (uBTC)

```
https://www.universal.xyz/wrapped-tokens/UA-BTC.svg
```

![uBTC Logo](https://www.universal.xyz/wrapped-tokens/UA-BTC.svg)

### Ethereum (uETH)

```
https://www.universal.xyz/wrapped-tokens/UA-ETH.svg
```

![uETH Logo](https://www.universal.xyz/wrapped-tokens/UA-ETH.svg)

### Solana (uSOL)

```
https://www.universal.xyz/wrapped-tokens/UA-SOL.svg
```

![uSOL Logo](https://www.universal.xyz/wrapped-tokens/UA-SOL.svg)

## Supported Assets

All 80+ uAssets have corresponding logos. Some examples:

| Asset | Logo URL |
|-------|----------|
| uBTC | [https://www.universal.xyz/wrapped-tokens/UA-BTC.svg](https://www.universal.xyz/wrapped-tokens/UA-BTC.svg) |
| uETH | [https://www.universal.xyz/wrapped-tokens/UA-ETH.svg](https://www.universal.xyz/wrapped-tokens/UA-ETH.svg) |
| uSOL | [https://www.universal.xyz/wrapped-tokens/UA-SOL.svg](https://www.universal.xyz/wrapped-tokens/UA-SOL.svg) |
| uDOGE | [https://www.universal.xyz/wrapped-tokens/UA-DOGE.svg](https://www.universal.xyz/wrapped-tokens/UA-DOGE.svg) |
| uXRP | [https://www.universal.xyz/wrapped-tokens/UA-XRP.svg](https://www.universal.xyz/wrapped-tokens/UA-XRP.svg) |
| uADA | [https://www.universal.xyz/wrapped-tokens/UA-ADA.svg](https://www.universal.xyz/wrapped-tokens/UA-ADA.svg) |
| uDOT | [https://www.universal.xyz/wrapped-tokens/UA-DOT.svg](https://www.universal.xyz/wrapped-tokens/UA-DOT.svg) |
| uNEAR | [https://www.universal.xyz/wrapped-tokens/UA-NEAR.svg](https://www.universal.xyz/wrapped-tokens/UA-NEAR.svg) |
| uLINK | [https://www.universal.xyz/wrapped-tokens/UA-LINK.svg](https://www.universal.xyz/wrapped-tokens/UA-LINK.svg) |
| uUNI | [https://www.universal.xyz/wrapped-tokens/UA-UNI.svg](https://www.universal.xyz/wrapped-tokens/UA-UNI.svg) |

See [Smart Contracts](smart-contracts.md) for the complete list of supported assets.

## Integration Examples

### React Component

```typescript
interface TokenLogoProps {
  symbol: string;
  size?: number;
}

function TokenLogo({ symbol, size = 24 }: TokenLogoProps) {
  const logoUrl = `https://www.universal.xyz/wrapped-tokens/UA-${symbol}.svg`;
  
  return (
    <img
      src={logoUrl}
      alt={`u${symbol} logo`}
      width={size}
      height={size}
      onError={(e) => {
        e.currentTarget.src = '/fallback-logo.svg';
      }}
    />
  );
}

// Usage
<TokenLogo symbol="BTC" size={32} />
```

### Dynamic Logo Loading

```typescript
function getTokenLogoUrl(symbol: string): string {
  return `https://www.universal.xyz/wrapped-tokens/UA-${symbol}.svg`;
}

// Example usage in a token list
const tokens = ['BTC', 'ETH', 'SOL', 'DOGE'];

tokens.forEach(symbol => {
  const logoUrl = getTokenLogoUrl(symbol);
  console.log(`${symbol} logo:`, logoUrl);
});
```

### HTML

```html
<!-- Bitcoin -->
<img 
  src="https://www.universal.xyz/wrapped-tokens/UA-BTC.svg" 
  alt="uBTC Logo" 
  width="24" 
  height="24"
/>

<!-- Ethereum -->
<img 
  src="https://www.universal.xyz/wrapped-tokens/UA-ETH.svg" 
  alt="uETH Logo" 
  width="24" 
  height="24"
/>

<!-- Solana -->
<img 
  src="https://www.universal.xyz/wrapped-tokens/UA-SOL.svg" 
  alt="uSOL Logo" 
  width="24" 
  height="24"
/>
```

## Logo Specifications

- **Format**: SVG (Scalable Vector Graphics)
- **Scaling**: Logos are vector-based and scale perfectly to any size
- **Background**: Transparent background
- **Colors**: Full color logos matching brand guidelines

## Best Practices

### Error Handling

Always implement fallback logic for missing or failed logo loads:

```typescript
function TokenImage({ symbol }: { symbol: string }) {
  const [error, setError] = useState(false);
  const logoUrl = `https://www.universal.xyz/wrapped-tokens/UA-${symbol}.svg`;
  
  if (error) {
    return <div className="fallback-logo">{symbol}</div>;
  }
  
  return (
    <img
      src={logoUrl}
      alt={`u${symbol}`}
      onError={() => setError(true)}
    />
  );
}
```

### Caching

Consider caching logo URLs to improve performance:

```typescript
const logoCache = new Map<string, string>();

function getCachedLogoUrl(symbol: string): string {
  if (!logoCache.has(symbol)) {
    logoCache.set(
      symbol,
      `https://www.universal.xyz/wrapped-tokens/UA-${symbol}.svg`
    );
  }
  return logoCache.get(symbol)!;
}
```

### Accessibility

Always include proper alt text:

```typescript
<img
  src={logoUrl}
  alt={`${tokenName} (u${symbol}) logo`}
  aria-label={`${tokenName} token logo`}
/>
```

## License & Usage

Universal asset logos are provided for use in applications integrating with Universal Protocol. Please:

- Use logos only in context of Universal asset trading/display
- Don't modify or alter the logos
- Maintain visual quality and proportions
- Include proper attribution when required

For brand guidelines or commercial usage questions:

📩 **Email:** austin@universal.xyz

## Resources

- [Smart Contracts](smart-contracts.md) - Full asset list
- [API Documentation](api.md) - API integration
- [Brand Kit](../resources/brand-kit.md) - Additional brand assets

## Support

- [Discord](http://discord.gg/universalassets)
- [Contact Team](../introduction/core-contributors.md)
