# useBalance & useBalances

Fetching the native Substrate-based token balance. This hook is useful for verifying whether a user’s account has sufficient funds to cover transaction fees.

### Usage

```tsx

import { useBalance, useBalances, Balance } from 'typink';

const ALICE = '5GrwvaEF5zXb26Fz9rcQpDWS57CtERHpNehXCPcNoHGKutQY';
const BOB = '5FHneW46xGXgs5mUiveU4sbTyGBzmstUspZC92UhjJM694ty';

const balance: Balance | undefined = useBalance(ALICE);
const balances: Record<string, Balance> = useBalances([ALICE, BOB]);

// ...
```
