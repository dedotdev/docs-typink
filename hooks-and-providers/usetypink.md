# useTypink

## useTypink Hook

The `useTypink` hook is the main hook for accessing all Typink functionality in your React components. It provides access to network clients, wallet management, account information, and contract deployments.

### Overview

`useTypink` combines multiple contexts into a single convenient hook, giving you access to:

* Network connections and Dedot client instances
* Wallet connections and account management
* Contract deployment information
* Transaction signing capabilities

### Import & Basic Usage

```tsx
import { useTypink } from 'typink';

function MyComponent() {
  const {
    // Network & Client
    ready,
    client,
    network,

    // Wallet & Account
    connectedAccount,
    accounts,
    wallets,

    // Contract
    deployments,
  } = useTypink();

  // Your component logic
}
```

### Properties

#### A. Network & Client Management

<table><thead><tr><th width="210.44140625">Name</th><th>Type</th><th>Description</th></tr></thead><tbody><tr><td><code>ready</code></td><td><code>boolean</code></td><td>Indicates whether all network clients are connected and ready for blockchain interactions.</td></tr><tr><td><code>client</code></td><td><code>CompatibleSubstrateApi&#x3C;ChainApi>?</code></td><td>The primary Dedot client instance for the main network.</td></tr><tr><td><code>network</code></td><td><code>NetworkInfo</code></td><td>Primary network information (ID, name, RPC providers, token symbol, decimals).</td></tr><tr><td><code>networkConnection</code></td><td><code>NetworkConnection</code></td><td>Primary network connection details (network ID and provider type).</td></tr><tr><td><code>networks</code></td><td><code>NetworkInfo[]</code></td><td>All network info objects (primary first, then secondary networks).</td></tr><tr><td><code>networkConnections</code></td><td><code>NetworkConnection[]</code></td><td>All network connections (primary first, then secondary).</td></tr><tr><td><code>clients</code></td><td><code>Map&#x3C;NetworkId, CompatibleSubstrateApi&#x3C;ChainApi>></code></td><td>Map of Dedot client instances indexed by network ID for multi-network access.</td></tr><tr><td><code>connectionStatus</code></td><td><code>Map&#x3C;NetworkId, ClientConnectionStatus></code></td><td>Connection status for each network (<code>NotConnected</code>, <code>Connecting</code>, or <code>Connected</code>).</td></tr><tr><td><code>setNetwork</code></td><td><code>(connection: NetworkId | NetworkConnection) => void</code></td><td>Switch to a different primary network.</td></tr><tr><td><code>setNetworks</code></td><td><code>(networks: (NetworkId | NetworkConnection)[]) => void</code></td><td>Configure multiple network connections at once (first element becomes primary).</td></tr></tbody></table>

#### B. Wallet & Account Management

<table><thead><tr><th width="208.3125">Name</th><th>Type</th><th>Description</th></tr></thead><tbody><tr><td><code>appName</code></td><td><code>string</code></td><td>Your dApp name as configured in TypinkProvider.</td></tr><tr><td><code>wallets</code></td><td><code>Wallet[]</code></td><td>All available/configured wallet extensions with their installation and ready status.</td></tr><tr><td><code>connectedWalletIds</code></td><td><code>string[]</code></td><td>IDs of currently connected wallets.</td></tr><tr><td><code>connectedWallets</code></td><td><code>Wallet[]</code></td><td>Wallet objects for all currently connected wallets.</td></tr><tr><td><code>accounts</code></td><td><code>TypinkAccount[]</code></td><td>All accounts from all connected wallets.</td></tr><tr><td><code>connectedAccount</code></td><td><code>TypinkAccount?</code></td><td>Currently active account used for signing transactions and contract queries.</td></tr><tr><td><code>signer</code></td><td><code>InjectedSigner?</code></td><td>Signer object for transaction signing (automatically managed by Typink).</td></tr><tr><td><code>connectWallet</code></td><td><code>(id: string) => Promise&#x3C;void></code></td><td>Connect to a wallet by its ID (e.g., <code>'subwallet-js'</code>, <code>'talisman'</code>).</td></tr><tr><td><code>disconnect</code></td><td><code>(walletId?: string) => void</code></td><td>Disconnect from a specific wallet or all wallets if no ID provided.</td></tr><tr><td><code>setConnectedAccount</code></td><td><code>(account: TypinkAccount) => void</code></td><td>Switch the currently active account.</td></tr></tbody></table>

#### C. Contract & Deployment

<table><thead><tr><th width="175.15625">Name</th><th width="220.72265625">Type</th><th>Description</th></tr></thead><tbody><tr><td><code>deployments</code></td><td><code>ContractDeployment[]</code></td><td>All registered contract deployments with their addresses, metadata, and network info.</td></tr><tr><td><code>defaultCaller</code></td><td><code>SubstrateAddress</code></td><td>Default address for read-only queries when no wallet is connected.</td></tr></tbody></table>
