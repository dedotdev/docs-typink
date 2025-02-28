# useBalance & useBalances

{% hint style="info" %}
WIP  🚧
{% endhint %}

### Usage

```tsx

import { useBalance, useBalances, Balance } from 'typink';

const ALICE = '5GrwvaEF5zXb26Fz9rcQpDWS57CtERHpNehXCPcNoHGKutQY';
const BOB = '5FHneW46xGXgs5mUiveU4sbTyGBzmstUspZC92UhjJM694ty';

const balance: Balance | undefined = useBalance(ALICE);
const balances: Record<string, Balance> = useBalances([ALICE, BOB]);

// ...
```
