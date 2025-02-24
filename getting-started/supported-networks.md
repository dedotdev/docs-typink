# Supported networks

Typink comes with existing information for some well-known ink! contract networks ready to use out of the box, but you can always define and register your own custom network into Typink.

## Well-known ink! contracts supported networks

### Mainnet

* `alephZero`
* `astar`
* `shiden`

```tsx
import { alephZero, astar, shiden } from 'typink'

<TypinkProvider
  ...
  supportedNetworks={[alephZero, astar, shiden]}
  defaultNetworkId={alephZero.id}>
  <MyAppComponent ... />
</TypinkProvider>
```

### Testnet

* `popTestnet`
* `alephZeroTestnet`
* `shibuyaTestnet`

```tsx
import { popTestnet, alephZeroTestnet, shibuyaTestnet } from 'typink'

<TypinkProvider
  ...
  supportedNetworks={[popTestnet, alephZeroTestnet, shibuyaTestnet]}
  defaultNetworkId={popTestnet.id}>
  <MyAppComponent ... />
</TypinkProvider>
```

### Development

* `development`

```tsx
import { development } from 'typink'

<TypinkProvider
  ...
  supportedNetworks={[development]}
  defaultNetworkId={development.id}>
  <MyAppComponent ... />
</TypinkProvider>
```

## Define & register custom network info

You can define a new network info following the `NetworkInfo`interface:

```typescript
type NetworkId = string;

enum JsonRpcApi {
  LEGACY = 'legacy',
  NEW = 'new',
}

interface NetworkInfo {
  id: NetworkId;
  name: string;
  logo: string;
  providers: string[];
  symbol: string;
  decimals: number;
  subscanUrl?: string;
  pjsUrl?: string;
  faucetUrl?: string;
  jsonRpcApi?: JsonRpcApi; // default to new
}
```

For example, let's add a custom network:

```typescript
import { NetworkInfo } from 'typink';
const myNetwork: NetworkInfo = {
  id: 'my_network';
  name: 'My Network';
  logo: 'https://path/to/your/network/logo';
  providers: ['wss://mynetwork.dev'];
  symbol: 'MYN';
  decimals: 12;
}
```

Now we register `myNetwork`into Typink to connect with the network&#x20;

```tsx
<TypinkProvider
  ...
  supportedNetworks={[myNetwork]}
  defaultNetworkId={myNetwork.id}>
  <MyAppComponent ... />
</TypinkProvider>
```

