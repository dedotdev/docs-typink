# useRootStorage

**⚠️ ink! v5 and v6 only (requires ink! ABI)**

Fetches and manages the entire root storage structure of an ink! smart contract with type-safe access to all storage fields. Supports automatic refresh on new blocks.

#### Props

<table><thead><tr><th width="160.28125">Name</th><th>Type</th><th>Description</th></tr></thead><tbody><tr><td><code>contract</code></td><td><code>Contract&#x3C;T> | undefined</code></td><td>The contract instance to fetch root storage from.</td></tr><tr><td><code>watch</code></td><td><code>boolean?</code></td><td>Whether to watch for block changes and automatically refresh the storage. Default: <code>false</code>.</td></tr></tbody></table>

#### Return Type

<table><thead><tr><th width="161.48828125">Name</th><th>Type</th><th>Description</th></tr></thead><tbody><tr><td><code>storage</code></td><td><code>RootStorage | undefined</code></td><td>The root storage data, fully typed based on the contract's storage structure.</td></tr><tr><td><code>isLoading</code></td><td><code>boolean</code></td><td>Whether the initial storage fetch is in progress.</td></tr><tr><td><code>isRefreshing</code></td><td><code>boolean</code></td><td>Whether a manual refresh is in progress.</td></tr><tr><td><code>error</code></td><td><code>Error | undefined</code></td><td>Any error that occurred during storage fetching.</td></tr><tr><td><code>refresh</code></td><td><code>() => Promise&#x3C;void></code></td><td>Function to manually refresh the storage data.</td></tr></tbody></table>

#### Basic Usage

**Access token metadata:**

```tsx
import { useContract, useRootStorage } from 'typink';
import { Psp22ContractApi } from './types/psp22';

function TokenInfo() {
  const { contract } = useContract<Psp22ContractApi>('psp22-token');
  const { storage, isLoading, refresh } = useRootStorage({ contract });

  if (isLoading) return <div>Loading token info...</div>;

  return (
    <div>
      <p>Name: {storage?.name}</p>
      <p>Symbol: {storage?.symbol}</p>
      <p>Decimals: {storage?.decimals}</p>
      <p>Total Supply: {storage?.data?.totalSupply?.toString()}</p>
      <button onClick={refresh}>Refresh</button>
    </div>
  );
}
```

**With watch mode:**

```tsx
const { storage, isLoading } = useRootStorage({
  contract,
  watch: true, // Auto-update on new blocks
});

// Access nested storage fields
const totalSupply = storage?.data?.totalSupply;
const tokenName = storage?.name;
```
