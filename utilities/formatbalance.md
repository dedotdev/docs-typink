# formatBalance

Format a balance value to a human-readable string

```tsx
import { formatBalance, popTestnet } from 'typink';

formatBalance(1e12, popTestnet); // 100 PAS

const options = { decimals: 12, symbol: 'UNIT' };
formatBalance('12023172837123', options) // 12.0231 UNIT;
formatBalance(12_023_172_837_123n, options) // 12.0231 UNIT;
formatBalance(BigInt(12_023_172_837_123), options // 12.0231 UNIT;
```

