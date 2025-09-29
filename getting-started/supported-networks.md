# Supported networks

## Supported Networks

Typink comes with built-in support for many well-known Substrate networks, ready to use out of the box. You can also define and register your own custom networks.

Typink supports two types of smart contract platforms:

* **ink! v4/v5 (WASM)** - Contracts running on `pallet-contracts` using WebAssembly
* **ink! v6/Solidity (PolkaVM)** - Contracts running on `pallet-revive` using PolkaVM

### Smart Contract Support Overview

<table><thead><tr><th width="194.78125">Contract Type</th><th width="218.97265625">Technology</th><th>Networks</th></tr></thead><tbody><tr><td><strong>ink! v4/v5</strong></td><td>WASM (pallet-contracts)</td><td>alephZero, astar, shiden, popTestnet, alephZeroTestnet, shibuyaTestnet</td></tr><tr><td><strong>ink! v6 / Solidity</strong></td><td>PolkaVM (pallet-revive)</td><td>popTestnet, passetHub, westendAssetHub, kusamaAssetHub</td></tr></tbody></table>

**Note:** `popTestnet` supports both contract types.

### Well-Known Supported Networks

#### Mainnet

Typink provides built-in support for the following mainnet networks:

**ink! v4/v5 Networks (WASM, pallet-contracts)**

* `alephZero` - Aleph Zero
* `astar` - Astar
* `shiden` - Shiden

**ink! v6/Solidity Networks (PolkaVM, pallet-revive)**

* `kusamaAssetHub` - Kusama Asset Hub

**Polkadot**

* `polkadot` - Polkadot Relay Chain
* `polkadotAssetHub` - Polkadot Asset Hub
* `polkadotPeople` - Polkadot People Chain

**Kusama**

* `kusama` - Kusama Relay Chain
* `kusamaPeople` - Kusama People Chain

**Other Networks**

* `hydration` - Hydration
* `basilisk` - Basilisk
* `vara` - Vara Network

**Example Usage:**

```tsx
import {
  alephZero,
  astar,
  kusamaAssetHub,
  polkadot,
  kusama
} from 'typink';

// Example 1: Mix of contract and general Substrate networks
<TypinkProvider
  supportedNetworks={[alephZero, astar, polkadot, kusama]}
  defaultNetworkId={alephZero.id}>
  <MyAppComponent />
</TypinkProvider>

// Example 2: PolkaVM contract network with general Substrate
<TypinkProvider
  supportedNetworks={[kusamaAssetHub, kusama]}
  defaultNetworkId={kusamaAssetHub.id}>
  <MyAppComponent />
</TypinkProvider>
```

#### Testnet

Typink provides built-in support for the following testnet networks:

**ink! v4/v5 Testnets (WASM, pallet-contracts)**

* `popTestnet` - POP Network Testnet (supports both WASM and PolkaVM)
* `alephZeroTestnet` - Aleph Zero Testnet
* `shibuyaTestnet` - Shibuya Testnet

**ink! v6/Solidity Testnets (PolkaVM, pallet-revive)**

* `popTestnet` - POP Network Testnet (supports both WASM and PolkaVM)
* `passetHub` - Passet Hub
* `westendAssetHub` - Westend Asset Hub

**Westend**

* `westend` - Westend Testnet
* `westendPeople` - Westend People Chain

**Paseo**

* `paseo` - Paseo Testnet
* `paseoPeople` - Paseo People Chain
* `paseoAssetHub` - Paseo Asset Hub
* `paseoHydration` - Paseo Hydration

**Example Usage:**

```tsx
import {
  popTestnet,
  alephZeroTestnet,
  shibuyaTestnet,
  passetHub,
  westendAssetHub,
  westend,
  paseo
} from 'typink';

// Example 1: ink! v4/v5 (WASM) contract networks
<TypinkProvider
  supportedNetworks={[popTestnet, alephZeroTestnet, shibuyaTestnet]}
  defaultNetworkId={popTestnet.id}>
  <MyAppComponent />
</TypinkProvider>

// Example 2: ink! v6/Solidity (PolkaVM) contract networks
<TypinkProvider
  supportedNetworks={[popTestnet, passetHub, westendAssetHub]}
  defaultNetworkId={popTestnet.id}>
  <MyAppComponent />
</TypinkProvider>

// Example 3: Mix of contract and general Substrate networks
<TypinkProvider
  supportedNetworks={[popTestnet, westend, paseo]}
  defaultNetworkId={popTestnet.id}>
  <MyAppComponent />
</TypinkProvider>
```

#### Development

For local development with a Substrate node:

* `development` - Local Development Network (ws://127.0.0.1:9944)

**Example Usage:**

```tsx
import { development } from 'typink';

<TypinkProvider
  supportedNetworks={[development]}
  defaultNetworkId={development.id}>
  <MyAppComponent />
</TypinkProvider>
```

### Define & Register Custom Network Info

You can define a new network info following the `NetworkInfo` interface:

```typescript
type NetworkId = string;

enum NetworkType {
  DEVNET = 'devnet',
  TESTNET = 'testnet',
  MAINNET = 'mainnet',
}

enum JsonRpcApi {
  LEGACY = 'legacy',
  NEW = 'new',
}

interface NetworkInfo {
  id: NetworkId;
  type?: NetworkType;                    // default to mainnet
  name: string;
  logo: string;
  providers: string[];
  symbol: string;
  decimals: number;
  subscanUrl?: string;
  pjsUrl?: string;
  faucetUrl?: string;
  jsonRpcApi?: JsonRpcApi;              // default to new
  chainSpec?: () => Promise<string>;     // for light client support
  relayChain?: NetworkInfo;              // for parachains using light client
}
```

#### Basic Custom Network Example

For a basic custom network:

```typescript
import { NetworkInfo, NetworkType } from 'typink';

const myNetwork: NetworkInfo = {
  id: 'my_network',
  type: NetworkType.MAINNET,
  name: 'My Network',
  logo: 'https://path/to/your/network/logo.png',
  providers: ['wss://mynetwork.dev'],
  symbol: 'MYN',
  decimals: 12,
};
```

#### Custom Network with Light Client Support

To add light client support to your custom network:

```typescript
import { NetworkInfo, NetworkType } from 'typink';

const myNetwork: NetworkInfo = {
  id: 'my_network',
  type: NetworkType.MAINNET,
  name: 'My Network',
  logo: 'https://path/to/your/network/logo.png',
  providers: ['wss://mynetwork.dev'],
  symbol: 'MYN',
  decimals: 12,
  chainSpec: async () => {
    // Option 1: Use known chain from @substrate/connect-known-chains
    return (await import('@substrate/connect-known-chains/polkadot')).chainSpec;

    // Option 2: Fetch from URL
    // const response = await fetch('https://example.com/chainspec.json');
    // return await response.text();

    // Option 3: Inline chain spec
    // return '{"name":"MyChain","id":"my_chain",...}';
  },
};
```

#### Custom Parachain with Light Client Support

For parachains, you must specify the relay chain:

```typescript
import { NetworkInfo, NetworkType, polkadot } from 'typink';

const myParachain: NetworkInfo = {
  id: 'my_parachain',
  type: NetworkType.MAINNET,
  name: 'My Parachain',
  logo: 'https://path/to/your/parachain/logo.png',
  providers: ['wss://myparachain.dev'],
  symbol: 'MPC',
  decimals: 12,
  chainSpec: async () => {
    const response = await fetch('https://myparachain.dev/chainspec.json');
    return await response.text();
  },
  relayChain: polkadot, // Reference to the relay chain NetworkInfo
};
```

#### Register Custom Network

Once defined, register your custom network with Typink:

```tsx
<TypinkProvider
  supportedNetworks={[myNetwork]}
  defaultNetworkId={myNetwork.id}>
  <MyAppComponent />
</TypinkProvider>
```

You can also combine custom networks with built-in networks:

```tsx
import { popTestnet, alephZero } from 'typink';

<TypinkProvider
  supportedNetworks={[myNetwork, popTestnet, alephZero]}
  defaultNetworkId={myNetwork.id}>
  <MyAppComponent />
</TypinkProvider>
```

### Network Properties

#### Required Properties

* `id` - Unique network identifier
* `name` - Human-readable network name
* `logo` - URL to network logo
* `providers` - Array of RPC endpoint URLs
* `symbol` - Native token symbol
* `decimals` - Native token decimals

#### Optional Properties

* `type` - Network type (mainnet, testnet, devnet)
* `subscanUrl` - Subscan explorer URL
* `pjsUrl` - Polkadot.js Apps URL
* `faucetUrl` - Faucet URL for testnets
* `jsonRpcApi` - RPC API version (new or legacy)
* `chainSpec` - Chain specification for light client support
* `relayChain` - Parent relay chain for parachains (required for parachain light clients)
