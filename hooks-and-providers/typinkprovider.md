# TypinkProvider

## TypinkProvider

`TypinkProvider` is the main provider component for Typink DApps. It manages shared state internally so hooks and child components can access accounts, signers, wallet connections, Dedot clients, contract deployments, and more.

The first step is to wrap your application component with `TypinkProvider`.

### Basic Usage

```tsx
import { popTestnet, alephZeroTestnet } from 'typink';

const SUPPORTED_NETWORKS = [popTestnet, alephZeroTestnet];

<TypinkProvider
  supportedNetworks={SUPPORTED_NETWORKS}
  defaultNetworkId={popTestnet.id}
  cacheMetadata={true}
  appName="My DApp">
  <MyAppComponent />
</TypinkProvider>
```

### Multi-Network Support

TypinkProvider supports connecting to multiple networks simultaneously. Use `defaultNetworkIds` (plural) to specify multiple default networks:

```tsx
<TypinkProvider
  deployments={deployments}
  supportedNetworks={SUPPORTED_NETWORKS}
  defaultNetworkIds={[
    popTestnet.id,              // Primary network
    alephZeroTestnet.id         // Secondary network
  ]}
  appName="My DApp">
  <MyAppComponent />
</TypinkProvider>
```

You can also specify custom providers per network using the `NetworkConnection` type:

```tsx
<TypinkProvider
  deployments={deployments}
  supportedNetworks={SUPPORTED_NETWORKS}
  defaultNetworkIds={[
    { networkId: popTestnet.id, provider: 'wss://rpc.polkadot.io' },
    { networkId: alephZeroTestnet.id, provider: 'random-rpc' }
  ]}
  appName="My DApp">
  <MyAppComponent />
</TypinkProvider>
```

### Light Client Support

TypinkProvider supports connecting to networks via a light client using [Smoldot](https://github.com/smol-dot/smoldot), which provides a trustless, direct P2P connection to the blockchain without relying on centralized RPC providers.

#### Benefits

* **Trustless**: Directly verify blockchain data without trusting RPC providers
* **Decentralized**: Connect via P2P networking instead of centralized endpoints
* **Resilient**: No single point of failure from RPC provider downtime
* **Privacy**: No third-party tracking of your RPC requests

#### How to Use Light Client

To connect using a light client, specify `'light-client'` as the provider in your network configuration:

```tsx
import { polkadot, kusama } from 'typink';

<TypinkProvider
  supportedNetworks={[polkadot, kusama]}
  defaultNetworkIds={[
    { networkId: polkadot.id, provider: 'light-client' }
  ]}
  appName="My DApp">
  <MyAppComponent />
</TypinkProvider>
```

You can also mix light client connections with traditional RPC connections:

```tsx
<TypinkProvider
  supportedNetworks={[polkadot, westend]}
  defaultNetworkIds={[
    { networkId: polkadot.id, provider: 'light-client' },        // Use light client
    { networkId: westend.id, provider: 'wss://westend-rpc.polkadot.io' }  // Use RPC
  ]}
  appName="My DApp">
  <MyAppComponent />
</TypinkProvider>
```

#### Networks with Light Client Support

Only networks that include a `chainSpec` property support light client connections. The following networks have built-in light client support:

**Mainnet Networks**

* `polkadot` - Polkadot
* `polkadotAssetHub` - Polkadot Asset Hub
* `polkadotPeople` - Polkadot People
* `kusama` - Kusama
* `kusamaAssetHub` - Kusama Asset Hub
* `kusamaPeople` - Kusama People

**Testnet Networks**

* `westend` - Westend
* `westendAssetHub` - Westend Asset Hub
* `westendPeople` - Westend People
* `paseo` - Paseo

**Note:** Attempting to use `'light-client'` as a provider for networks without `chainSpec` will throw an error: `"Network does not support light client - missing chainSpec"`.

#### Adding Light Client Support to Custom Networks

You can add light client support to any network by providing a `chainSpec` property in your `NetworkInfo` object. The `chainSpec` should be an async function that returns the chain specification string.

**Using Known Chains from @substrate/connect-known-chains**

For well-known Substrate chains, you can use the `@substrate/connect-known-chains` package:

```tsx
import { NetworkInfo, NetworkType } from 'typink';

const myCustomPolkadotNetwork: NetworkInfo = {
  id: 'my_custom_network',
  type: NetworkType.MAINNET,
  name: 'My Custom Network',
  logo: 'https://example.com/logo.png',
  providers: ['wss://rpc.example.com'],
  symbol: 'CUSTOM',
  decimals: 10,
  chainSpec: async () => {
    return (await import('@substrate/connect-known-chains/polkadot')).chainSpec;
  },
};
```

**Using Custom Chain Spec**

For custom networks, you can fetch the chain spec from a URL or provide it directly:

```tsx
const myParachainNetwork: NetworkInfo = {
  id: 'my_parachain',
  type: NetworkType.MAINNET,
  name: 'My Parachain',
  logo: 'https://example.com/logo.png',
  providers: ['wss://rpc.myparachain.com'],
  symbol: 'MPC',
  decimals: 12,
  chainSpec: async () => {
    // Fetch from URL
    const response = await fetch('https://example.com/chainspec.json');
    return await response.text();

    // Or return inline
    // return '{"name":"MyChain","id":"my_chain",...}';
  },
  relayChain: polkadot, // Required for parachains - reference to the relay chain NetworkInfo
};
```

**For Parachains:** When adding light client support for parachains, you must:

1. Provide the parachain's `chainSpec`
2. Set the `relayChain` property to reference the relay chain's `NetworkInfo` object
3. Ensure the relay chain also has a `chainSpec` defined

The light client will automatically handle the relay chain connection when connecting to the parachain.

#### Provider Types

The `provider` field in `NetworkConnection` accepts the following types:

* `'light-client'` - Use Smoldot light client for P2P connection
* `'random-rpc'` - Randomly select from available RPC endpoints (default)
* `'wss://...'` - Specific WebSocket Secure endpoint
* `'ws://...'` - Specific WebSocket endpoint

### Built-in Wallet Connector

TypinkProvider includes a built-in Typink Wallet Connector that manages wallet connections, signers, and accounts internally. By default, it supports SubWallet, Talisman, and PolkadotJS:

```tsx
<TypinkProvider
  deployments={deployments}
  supportedNetworks={SUPPORTED_NETWORKS}
  defaultNetworkId={popTestnet.id}
  appName="My DApp"
  wallets={[subwallet, talisman, polkadotjs]}> {/* Optional: defaults to these three */}
  <MyAppComponent />
</TypinkProvider>
```

When using the built-in wallet connector, you don't need to pass `connectedAccount` or `signer` props—Typink manages them automatically.

### External Wallet Connector (SubConnect, Talisman Connect)

If you're using an external wallet connector like [SubConnect](https://github.com/Koniverse/SubConnect-v2) or [Talisman Connect](https://github.com/TalismanSociety/talisman-connect), you'll need to pass `connectedAccount` ([TypinkAccount](https://github.com/dedotdev/typink/blob/main/packages/typink/src/types.ts#L6-L10)) and `signer` ([Signer](https://github.com/polkadot-js/api/blob/42b9735c32671e4fac2a5b78283a7fcdec9ef912/packages/types/src/types/extrinsic.ts#L168-L183)) props:

```tsx
const { connectedAccount, signer } = ... // from SubConnect or Talisman Connect

<TypinkProvider
  deployments={deployments}
  supportedNetworks={SUPPORTED_NETWORKS}
  defaultNetworkId={popTestnet.id}
  cacheMetadata={true}
  connectedAccount={connectedAccount}
  signer={signer}
  appName="My DApp">
  <MyAppComponent />
</TypinkProvider>
```

**Note:** When using external wallet connectors, these props override Typink's internal wallet management. Make sure to pass both `connectedAccount` and `signer` for proper functionality.

### Props

| **Prop**              | **Type**                             | **Description**                                                                                                                                                                                   |
| --------------------- | ------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **deployments**       | `ContractDeployment[]`               | An array of contract deployments (optional, defaults to empty array)                                                                                                                              |
| **defaultCaller**     | `string`                             | The default Substrate address used as the default caller for making queries when no wallet is connected (optional, defaults to ALICE address: `5FTZ6n1wY3GBqEZ2DWEdspbTarvRnp8DM8x2YXbWubu7JN98`) |
| **supportedNetworks** | `NetworkInfo[]`                      | An array of [supported networks](https://docs.dedot.dev/typink/getting-started/supported-networks) for your dApp                                                                                  |
| **defaultNetworkIds** | `(NetworkId \| NetworkConnection)[]` | Default list of networks to use when no stored connections exist. First network is primary, rest are secondary. Supports custom providers per network.                                            |
| **defaultNetworkId**  | `NetworkId \| NetworkConnection`     | Fallback network if `defaultNetworkIds` is not provided. Converted to `defaultNetworkIds=[defaultNetworkId]` internally.                                                                          |
| **cacheMetadata**     | `boolean`                            | Toggle whether to cache network metadata (default: `false`)                                                                                                                                       |
| **appName**           | `string`                             | The name of your dApp, used to identify your dApp when connecting to wallets                                                                                                                      |
| **wallets**           | `Wallet[]`                           | Array of supported wallets. Defaults to `[subwallet, talisman, polkadotjs]`. Only used with built-in Typink Wallet Connector.                                                                     |
| **signer**            | `Signer`                             | The signer for handling transactions. If using an external wallet connector (e.g., SubConnect, Talisman Connect), pass your signer here to override Typink's internal signer management.          |
| **connectedAccount**  | `TypinkAccount`                      | The currently connected account. If using an external wallet connector, pass the active account here to inform Typink which account to use for interactions.                                      |
