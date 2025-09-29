# usePolkadotClient

Accesses a specific Dedot client instance by network ID. Returns the client, connection status, and network information. If no network ID is provided, returns the primary network's client.

#### Props

<table><thead><tr><th width="147.89453125">Name</th><th width="172.86328125">Type</th><th>Description</th></tr></thead><tbody><tr><td><code>networkId</code></td><td><code>NetworkId?</code></td><td>The network ID to get the client for. If not provided, returns the primary client.</td></tr></tbody></table>

#### Return Type

<table><thead><tr><th width="140.3671875">Name</th><th width="227.375">Type</th><th>Description</th></tr></thead><tbody><tr><td><code>client</code></td><td><code>CompatibleSubstrateApi&#x3C;ChainApi> | undefined</code></td><td>The Dedot client instance for the specified network.</td></tr><tr><td><code>status</code></td><td><code>ClientConnectionStatus</code></td><td>Connection status: <code>NotConnected</code>, <code>Connecting</code>, or <code>Connected</code>.</td></tr><tr><td><code>network</code></td><td><code>NetworkInfo</code></td><td>Network information (ID, name, RPC providers, token symbol, decimals).</td></tr></tbody></table>

#### Basic Usage

**Access primary client:**

```tsx
import { usePolkadotClient } from 'typink';

function ChainInfo() {
  const { client, status, network } = usePolkadotClient();

  if (status === 'Connecting') return <div>Connecting...</div>;
  if (status === 'NotConnected') return <div>Not connected</div>;

  return (
    <div>
      <p>Network: {network.name}</p>
      <p>Symbol: {network.symbol}</p>
      <p>Connected: {status === 'Connected' ? 'Yes' : 'No'}</p>
    </div>
  );
}
```

**Access specific network client:**

```tsx
const { client: polkadotClient, status } = usePolkadotClient('polkadot');
const { client: kusamaClient } = usePolkadotClient('kusama');

// Use for multi-network queries
if (polkadotClient && status === 'Connected') {
  const balance = await polkadotClient.query.system.account(address);
}
```
