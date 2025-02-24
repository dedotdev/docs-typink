# TypinkProvider

`TypinkProvider`: A global provider for Typink DApps, it managed shared state internally so hooks and child components can access (accounts, signer, wallet connection, Dedot clients, contract deployments ...)

Wrap your application component with `TypinkProvider`.

```tsx
import { popTestnet, development } from 'typink'
import { deployments } from './deployments';

// a default caller address when interact with ink! contract if there is no wallet is connected
const DEFAULT_CALLER = '5xxx...' 

const SUPPORTED_NETWORKS = [popTestnet]; // alephZeroTestnet, ...
if (process.env.NODE_ENV === 'development') {
  SUPPORTED_NETWORKS.push(development);
}

<TypinkProvider
  deployments={deployments}
  defaultCaller={DEFAULT_CALLER}
  supportedNetworks={SUPPORTED_NETWORKS}
  defaultNetworkId={popTestnet.id}
  cacheMetadata={true}>
  <MyAppComponent ... />
</TypinkProvider>
```

If you're using an external wallet connector like [SubConnect](https://github.com/Koniverse/SubConnect-v2) or [Talisman Connect](https://github.com/TalismanSociety/talisman-connect), you will need to pass into the `TypinkProvider` 2 more props: `connectedAccount` ([InjectedAccouunt](https://github.com/dedotdev/typink/blob/d10fe8b7fccc9ef7cdfc1b74576705c6f261160d/packages/typink/src/types.ts#L46-L51)) & `signer` ([Signer](https://github.com/polkadot-js/api/blob/42b9735c32671e4fac2a5b78283a7fcdec9ef912/packages/types/src/types/extrinsic.ts#L168-L183)) so Typink knows which account & signer to interact with the ink! contracts.

```tsx

const { connectedAccount, signer } = ... // from subconnect or talisman-connect ...

<TypinkProvider
  deployments={deployments}
  defaultCaller={DEFAULT_CALLER}
  supportedNetworks={SUPPORTED_NETWORKS}
  defaultNetworkId={popTestnet.id}
  cacheMetadata={true}
  connectedAccount={connectedAccount}
  signer={signer}
>
  <MyAppComponent ... />
</TypinkProvider>
```

