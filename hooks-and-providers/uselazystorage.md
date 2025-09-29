# useLazyStorage

**⚠️ ink! v5 and v6 only (requires ink! ABI)**

Fetches specific lazy storage values from ink! smart contracts with type-safe access to individual fields, mappings, vectors, and objects. Supports automatic refresh on new blocks.

#### Props

<table><thead><tr><th width="147.47265625">Name</th><th>Type</th><th>Description</th></tr></thead><tbody><tr><td><code>contract</code></td><td><code>Contract&#x3C;T> | undefined</code></td><td>The contract instance to fetch lazy storage from.</td></tr><tr><td><code>fn</code></td><td><code>(lazy: LazyStorage) => any</code></td><td>Function that navigates the lazy storage structure and returns the value to fetch.</td></tr><tr><td><code>watch</code></td><td><code>boolean?</code></td><td>Whether to watch for block changes and automatically refresh the data. Default: <code>false</code>.</td></tr></tbody></table>

#### Return Type

<table><thead><tr><th width="187.3046875">Name</th><th>Type</th><th>Description</th></tr></thead><tbody><tr><td><code>data</code></td><td><code>T | undefined</code></td><td>The fetched data, fully typed based on the accessor function result.</td></tr><tr><td><code>isLoading</code></td><td><code>boolean</code></td><td>Whether the initial data fetch is in progress.</td></tr><tr><td><code>isRefreshing</code></td><td><code>boolean</code></td><td>Whether a manual refresh is in progress.</td></tr><tr><td><code>error</code></td><td><code>Error | undefined</code></td><td>Any error that occurred during data fetching.</td></tr><tr><td><code>refresh</code></td><td><code>() => Promise&#x3C;void></code></td><td>Function to manually refresh the data.</td></tr></tbody></table>

#### Basic Usage

**Fetch balance from PSP22 mapping:**

```tsx
import { useContract, useLazyStorage } from 'typink';
import { Psp22ContractApi } from './types/psp22';

function UserBalance() {
  const { contract } = useContract<Psp22ContractApi>('psp22-token');
  const { connectedAccount } = useTypink();

  const { data: balance, isLoading } = useLazyStorage(
    connectedAccount?.address
      ? {
          contract,
          fn: (lazy) => lazy.data.balances.get(connectedAccount.address),
          watch: true, // Auto-update on new blocks
        }
      : undefined
  );

  if (isLoading) return <div>Loading balance...</div>;
  return <div>Balance: {balance?.toString()}</div>;
}
```

**Fetch with manual refresh:**

```tsx
const { data: balance, refresh, error } = useLazyStorage({
  contract,
  fn: (lazy) => lazy.data.balances.get(targetAddress),
  watch: false,
});

// Manually refresh after a transaction
await mintTx.signAndSend({ args: [amount] });
await refresh();
```
