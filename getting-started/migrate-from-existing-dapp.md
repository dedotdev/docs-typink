# Migrate from existing dapp

## Integrate Typink into Your DApp

This guide will walk you through integrating Typink into your dapp. Typink is designed to be flexible, making it easy to integrate whether you're building a new project or migrating an existing one—especially if your dapp already relies on external wallet connectors like SubConnect or Talisman Connect.

### Installation

First, install the required packages:

```bash
npm install typink dedot
# or
yarn add typink dedot
# or
pnpm add typink dedot
```

### Setup TypinkProvider

`TypinkProvider` is the main provider component that wraps your application and provides access to Typink's hooks and functionality.

#### Basic Setup

Wrap your application with `TypinkProvider` in your main entry point (e.g., `main.tsx` or `index.tsx`):

```tsx
import ReactDOM from 'react-dom/client';
import App from './App';
import { TypinkProvider, popTestnet, alephZeroTestnet } from 'typink';
import { deployments } from './contracts/deployments'; // Your contract deployments

const SUPPORTED_NETWORKS = [popTestnet, alephZeroTestnet];

function Root() {
  return (
    <TypinkProvider
      appName="My DApp"
      deployments={deployments}
      supportedNetworks={SUPPORTED_NETWORKS}
      defaultNetworkId={popTestnet.id}
      cacheMetadata={true}>
      <App />
    </TypinkProvider>
  );
}

const root = ReactDOM.createRoot(document.getElementById('root')!);
root.render(<Root />);
```

#### Key Props

* **`appName`**: The name of your dApp (used when connecting to wallets)
* **`deployments`**: Array of your contract deployments.
* **`supportedNetworks`**: Array of networks your dApp supports
* **`defaultNetworkId`**: The default network to connect to, or **`defaultNetworkIds`** if you want to connect to multiple networks at once
* **`cacheMetadata`**: Whether to cache network metadata (recommended: `true`)

For a complete list of props and advanced configuration (multi-network, light client, etc.), see the [TypinkProvider documentation](../hooks-and-providers/typinkprovider.md).

### Setup Wallet Connector

Typink offers flexibility in wallet integration with two main approaches:

#### Option 1: Built-in Typink Wallet Connector (Recommended for New Projects)

The built-in wallet connector manages wallet connections, signers, and accounts internally. It supports SubWallet, Talisman, and PolkadotJS by default.

```tsx
import {
  TypinkProvider,
  subwallet,
  talisman,
  polkadotjs,
  popTestnet
} from 'typink';

<TypinkProvider
  appName="My DApp"
  supportedNetworks={[popTestnet]}
  defaultNetworkId={popTestnet.id}
  wallets={[subwallet, talisman, polkadotjs]}> {/* Default wallets */}
  <App />
</TypinkProvider>
```

**Adding Custom Wallets**

You can also add custom wallet extensions:

```tsx
import { TypinkProvider, ExtensionWallet, subwallet, talisman } from 'typink';

const enkrypt = new ExtensionWallet({
  name: 'Enkrypt',
  id: 'enkrypt',
  logo: 'https://example.com/enkrypt-logo.png',
  installUrl: 'https://www.enkrypt.com',
  websiteUrl: 'https://www.enkrypt.com',
});

<TypinkProvider
  appName="My DApp"
  supportedNetworks={[popTestnet]}
  defaultNetworkId={popTestnet.id}
  wallets={[subwallet, talisman, enkrypt]}>
  <App />
</TypinkProvider>
```

**Using the useTypink Hook**

Once you've set up the built-in wallet connector, use the `useTypink` hook to access wallet state and actions in your components.

**Available Properties:**

```tsx
import { useTypink } from 'typink';

function MyComponent() {
  const {
    // Wallet & Account State
    wallets,              // Wallet[] - All available/configured wallets
    connectedWalletIds,   // string[] - Currently connected wallet IDs
    accounts,             // TypinkAccount[] - All accounts from connected wallets
    connectedAccount,     // TypinkAccount - Currently selected account
    signer,               // Signer - For signing transactions

    // Actions
    connectWallet,        // (id: string) => Promise<void>
    disconnect,           // (walletId?: string) => void
    setConnectedAccount,  // (account: TypinkAccount) => void
  } = useTypink();
}
```

**Example: Connect Wallet Button**

```tsx
import { useTypink } from 'typink';

function WalletConnect() {
  const { wallets, connectWallet, connectedWalletIds } = useTypink();

  return (
    <div>
      {wallets.map((wallet) => (
        <button
          key={wallet.id}
          onClick={() => connectWallet(wallet.id)}
          disabled={!wallet.installed}>
          <img src={wallet.logo} alt={wallet.name} width={24} />
          {wallet.name}
        </button>
      ))}
    </div>
  );
}
```

**Example: Account Selection & Disconnect**

```tsx
import { useTypink } from 'typink';

function AccountSelector() {
  const {
    accounts,
    connectedAccount,
    setConnectedAccount,
    disconnect
  } = useTypink();

  if (!connectedAccount) {
    return <WalletConnect />;
  }

  return (
    <div>
      <h3>Connected: {connectedAccount.name}</h3>
      <p>Address: {connectedAccount.address}</p>

      {/* Switch between accounts */}
      <select
        value={connectedAccount.address}
        onChange={(e) => {
          const account = accounts.find(a => a.address === e.target.value);
          if (account) setConnectedAccount(account);
        }}>
        {accounts.map((account) => (
          <option key={account.address} value={account.address}>
            {account.name} - {account.address.slice(0, 6)}...
          </option>
        ))}
      </select>

      {/* Disconnect */}
      <button onClick={() => disconnect()}>Disconnect</button>
    </div>
  );
}
```

**Key Points:**

* Call `connectWallet(id)` with a wallet's `id` property to connect
* `accounts` includes all accounts from all connected wallets
* Use `setConnectedAccount()` to switch between accounts
* Call `disconnect()` without arguments to disconnect all wallets, or pass a `walletId` to disconnect a specific wallet
* `connectedAccount` and `signer` are automatically managed and available for contract interactions

#### Option 2: External Wallet Connector (For Existing DApps)

If your dApp already uses SubConnect or Talisman Connect, you can easily integrate Typink by passing `signer` and `connectedAccount` props.

**SubConnect Integration**

```tsx
import { TypinkProvider } from 'typink';
import { useConnectWallet, Web3OnboardProvider } from '@subwallet-connect/react';
import { onboardWallets } from './onboard-wallets'; // Your SubConnect config

function TypinkApp() {
  const [{ wallet }] = useConnectWallet();
  // Get connectedAccount from your app state (e.g., localStorage, context)
  const { connectedAccount } = useYourAppState();

  return (
    <TypinkProvider
      supportedNetworks={[popTestnet]}
      defaultNetworkId={popTestnet.id}
      signer={wallet?.signer}
      connectedAccount={connectedAccount}>
      <App />
    </TypinkProvider>
  );
}

// Wrap with Web3OnboardProvider
<Web3OnboardProvider web3Onboard={onboardWallets}>
  <TypinkApp />
</Web3OnboardProvider>
```

**Talisman Connect Integration**

```tsx
import { TypinkProvider } from 'typink';
import { getWalletBySource } from '@talismn/connect-wallets';

function TypinkApp() {
  // Manage wallet connection in your app (e.g., via custom provider/context)
  const { wallet, connectedAccount } = useYourWalletState();

  return (
    <TypinkProvider
      supportedNetworks={[popTestnet]}
      defaultNetworkId={popTestnet.id}
      signer={wallet?.signer}
      connectedAccount={connectedAccount}>
      <App />
    </TypinkProvider>
  );
}

// Wrap with your wallet provider
<YourWalletProvider>
  <TypinkApp />
</YourWalletProvider>
```
