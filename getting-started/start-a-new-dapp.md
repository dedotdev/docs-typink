# Start a new dapp

## Getting Started with Typink

Typink is a React hooks library for building dApps that interact with ink! and Solidity smart contracts on Substrate-based blockchains. With Typink, you get a **unified developer experience** - the same hooks and APIs work seamlessly across ink! v5 (WASM), ink! v6 (PolkaVM), and Solidity contracts.

### Create a New Project

Start by creating a new Typink project using the interactive CLI:

{% tabs %}
{% tab title="pnpm" %}
```sh
pnpm create typink@latest
```
{% endtab %}

{% tab title="yarn" %}
```sh
yarn create typink@latest
```
{% endtab %}

{% tab title="bun" %}
```sh
bunx create typink@latest
```
{% endtab %}

{% tab title="npm" %}
```sh
npm create typink@latest
```
{% endtab %}

{% tab title="npx" %}
```sh
npx create-typink@latest
```
{% endtab %}
{% endtabs %}

The CLI will guide you through an interactive setup:

#### 1. Enter Project Name

```
? Project name: my-typink-dapp
```

#### 2. Select Contract Type

Choose the type of contracts you'll be working with:

```
? Select contract type:
❯ Ink! v6 (PolkaVM, pallet-revive)
  Ink! v6 using Sol ABI (PolkaVM, pallet-revive)
  Solidity (PolkaVM, pallet-revive)
  Ink! v5 (WASM, pallet-contracts)
```

**Contract Types:**

* **Ink! v6** - Latest ink! version on PolkaVM (pallet-revive)
* **Ink! v6 Sol ABI** - ink! v6 with Solidity-style ABI
* **Solidity** - Solidity smart contracts on PolkaVM
* **Ink! v5** - Legacy ink! on WASM (pallet-contracts)

#### 3. Select Networks

Choose one or more networks for your dApp:

```
? Select supported networks: (Press <space> to select)
❯◉ Pop Testnet
 ◯ Passet Hub
```

Available networks depend on your contract type:

* **pallet-contracts** (Ink! v5): Pop Testnet, Aleph Zero Testnet, Aleph Zero, Astar
* **pallet-revive** (Ink! v6/Solidity): Pop Testnet, Passet Hub

### Start Development

Navigate to your project and start the development server:

```bash
cd my-typink-dapp

# npm
npm run dev

# pnpm
pnpm dev

# yarn
yarn dev

# bun
bun dev
```

Open [http://localhost:3000](http://localhost:3000) to see your dApp running!

### Project Structure

Your project follows Next.js 15 App Router structure:

```
my-typink-dapp/
├── src/
│   ├── app/                    # Next.js app directory
│   │   ├── layout.tsx         # Root layout
│   │   ├── page.tsx           # Home page
│   │   └── globals.css        # Global styles
│   ├── components/            # React components
│   │   ├── shared/           # Shared UI components
│   │   └── [contract]-board.tsx  # Contract interaction components
│   ├── contracts/            # Contract-related files
│   │   ├── deployments.ts    # Contract deployment addresses
│   │   └── types/           # Generated TypeScript bindings
│   ├── lib/                  # Utility functions
│   │   └── utils.ts
│   └── providers/            # React providers
│       └── app-provider.tsx  # TypinkProvider configuration
├── public/                   # Static assets
├── package.json
├── tsconfig.json
└── next.config.ts
```

#### Key Files

**`src/providers/app-provider.tsx`** - Configures TypinkProvider with networks, wallets, and contracts:

```tsx
import { TypinkProvider, setupTxToaster, SonnerAdapter } from 'typink';
import { deployments } from '@/contracts/deployments';

setupTxToaster({
  adapter: new SonnerAdapter(toast),
});

export function AppProvider({ children }) {
  return (
    <TypinkProvider
      appName="My Typink dApp"
      deployments={deployments}
      supportedNetworks={[popTestnet]}
      defaultNetworkId={popTestnet.id}
      wallets={[subwallet, talisman, polkadotjs]}>
      {children}
    </TypinkProvider>
  );
}
```

**`src/contracts/deployments.ts`** - Registers contract deployments:

```tsx
export enum ContractId {
  FLIPPER = 'flipper',
}

export const deployments: ContractDeployment[] = [
  {
    id: ContractId.FLIPPER,
    metadata: flipperMetadata,
    address: '0x3ddc397c0350cbfb89d4f28d476073d6051067c4',
    network: popTestnet.id,
  },
];
```

### Explore the Example

Your project includes a pre-deployed example contract with working interactions:

1. **Connect Wallet** - Click "Connect Wallet" and select SubWallet, Talisman, or PolkadotJS
2. **View State** - See the current contract state (e.g., Flipper value, Storage value)
3. **Send Transactions** - Interact with the contract (e.g., flip the boolean, set storage)
4. **Watch Progress** - Transaction toasts show real-time progress

#### Example Contracts

* **Ink! v5**: Greeter contract (set and get messages)
* **Ink! v6**: Flipper contract (flip boolean value)
* **Ink! v6 Sol ABI**: Flipper contract with Solidity-style ABI
* **Solidity**: Storage contract (set and get uint256 value)

### Add Your Own Contracts

#### 1. Deploy Your Contract

Deploy your contract to a supported network using:

* [POP CLI](https://learn.onpop.io/contracts/guides/deploy) - Command-line tool for deploying to Pop Network
* [Contracts UI](https://ui.use.ink/) - User-friendly web interface
* [cargo-contract](https://github.com/paritytech/cargo-contract) - CLI tool
* [Remix IDE](https://remix.polkadot.io/) - For Solidity contracts on PolkaVM

You'll receive a contract address after successful deployment.

#### 2. Generate TypeScript Bindings

The project includes a pre-configured typegen script (`./scripts/typegen.ts`) that generates type-safe bindings for all contracts in `src/contracts/artifacts/`.

First, place your contract metadata/ABI files in `src/contracts/artifacts/`, then run:

```bash
# npm
npm run typegen

# pnpm
pnpm typegen

# yarn
yarn typegen

# bun
bun typegen
```

This script automatically processes all metadata/ABI files in `src/contracts/artifacts/` and generates TypeScript bindings to `src/contracts/types/`. The generated TypeScript API (e.g., `MyContractApi`) will be available in `src/contracts/types/my_contract/`.

#### 3. Register Contract Deployment

Add your contract to `src/contracts/deployments.ts`:

```tsx
import myContractMetadata from './artifacts/my_contract/metadata.json';

export enum ContractId {
  FLIPPER = 'flipper',
  MY_CONTRACT = 'my-contract', // Add your contract
}

export const deployments: ContractDeployment[] = [
  // ... existing deployments
  {
    id: ContractId.MY_CONTRACT,
    metadata: myContractMetadata,
    address: 'YOUR_CONTRACT_ADDRESS',
    network: popTestnet.id,
  },
];
```

### Unified Hooks - Works with All Contract Types

Typink's hooks provide a **unified API** that works identically across ink! v5, ink! v6, and Solidity contracts.

#### useContract - Initialize Contract Instance

Get a typed contract instance:

```tsx
import { useContract } from 'typink';
import { MyContractApi } from '@/contracts/types/my_contract';

function MyComponent() {
  const { contract } = useContract<MyContractApi>(ContractId.MY_CONTRACT);

  // contract is now fully typed based on your contract ABI
}
```

#### useContractQuery - Query Contract State

Read contract state with automatic type inference:

**Ink! v6 Flipper Example:**

```tsx
import { useContract, useContractQuery } from 'typink';
import { FlipperContractApi } from '@/contracts/types/flipper';

function FlipperQuery() {
  const { contract } = useContract<FlipperContractApi>(ContractId.FLIPPER);

  const { data: value, isLoading, refresh } = useContractQuery({
    contract,
    fn: 'get', // ✅ Fully typed - autocomplete works!
  });

  if (isLoading) return <div>Loading...</div>;

  return (
    <div>
      <p>Current value: {value?.toString()}</p>
      <button onClick={refresh}>Refresh</button>
    </div>
  );
}
```

**Solidity Storage Example:**

```tsx
import { useContract, useContractQuery } from 'typink';
import { StorageContractApi } from '@/contracts/types/storage';

function StorageQuery() {
  const { contract } = useContract<StorageContractApi>(ContractId.STORAGE);

  const { data: value, isLoading } = useContractQuery({
    contract,
    fn: 'retrieve', // ✅ Same API, different contract!
  });

  return <div>Stored value: {value?.toString()}</div>;
}
```

**💡 The hooks are identical!** Only the contract type and method names change.

#### useContractTx - Send Transactions

Execute contract transactions with the same API:

**Ink! v6 Flipper Example:**

```tsx
import { useContract, useContractTx, txToaster } from 'typink';

function FlipperTx() {
  const { contract } = useContract<FlipperContractApi>(ContractId.FLIPPER);
  const flipTx = useContractTx(contract, 'flip');

  const handleFlip = async () => {
    const toaster = txToaster('Flipping value...');

    try {
      await flipTx.signAndSend({
        callback: (result) => {
          toaster.onTxProgress(result);
        },
      });
    } catch (error) {
      toaster.onTxError(error);
    }
  };

  return (
    <button
      onClick={handleFlip}
      disabled={flipTx.inBestBlockProgress}>
      {flipTx.inBestBlockProgress ? 'Flipping...' : 'Flip'}
    </button>
  );
}
```

**Solidity Storage Example:**

```tsx
import { useContract, useContractTx, txToaster } from 'typink';

function StorageTx() {
  const { contract } = useContract<StorageContractApi>(ContractId.STORAGE);
  const storeTx = useContractTx(contract, 'store');
  const [value, setValue] = useState('');

  const handleStore = async () => {
    const toaster = txToaster('Storing value...');

    try {
      await storeTx.signAndSend({
        args: [BigInt(value)], // ✅ Type-safe args!
        callback: (result) => {
          toaster.onTxProgress(result);
        },
      });
    } catch (error) {
      toaster.onTxError(error);
    }
  };

  return (
    <>
      <input
        value={value}
        onChange={(e) => setValue(e.target.value)}
        type="number"
      />
      <button onClick={handleStore}>Store</button>
    </>
  );
}
```

**💡 Identical hook usage!** The only difference is the contract type and arguments.

#### useWatchContractEvent - Listen to Events

Watch for contract events with type-safe event data:

**Ink! v6 Example:**

```tsx
import { useContract, useWatchContractEvent } from 'typink';

function FlipperEvents() {
  const { contract } = useContract<FlipperContractApi>(ContractId.FLIPPER);
  const [events, setEvents] = useState([]);

  useWatchContractEvent(
    contract,
    'Flipped', // ✅ Event name is typed!
    useCallback((newEvents) => {
      newEvents.forEach((event) => {
        const { name, data } = event;
        console.log(`${name} event:`, data); // ✅ data is typed!
        setEvents((prev) => [...prev, event]);
      });
    }, [])
  );

  return (
    <div>
      <h3>Recent Events:</h3>
      {events.map((event, i) => (
        <div key={i}>{event.name}</div>
      ))}
    </div>
  );
}
```

**💡 Works the same for Solidity contracts!** Just use your Solidity contract's event names.

#### useDeployerTx - Deploy New Contracts

Deploy new contract instances:

```tsx
import { useDeployer, useDeployerTx, txToaster } from 'typink';
import { generateRandomHex } from 'dedot/utils';
import metadata from '@/contracts/types/flipper/metadata.json';

function DeployFlipper() {
  const wasm = metadata.source.wasm; // or metadata.source.hash
  const { deployer } = useDeployer<FlipperContractApi>(metadata, wasm);
  const newFlipperTx = useDeployerTx(deployer, 'new');

  const handleDeploy = async () => {
    const toaster = txToaster('Deploying contract...');

    try {
      await newFlipperTx.signAndSend({
        args: [false], // Initial value
        txOptions: { salt: generateRandomHex() },
        callback: (result, deployedAddress) => {
          toaster.onTxProgress(result);

          if (deployedAddress) {
            console.log('Deployed at:', deployedAddress);
          }
        },
      });
    } catch (error) {
      toaster.onTxError(error);
    }
  };

  return <button onClick={handleDeploy}>Deploy New Flipper</button>;
}
```

### Setup Transaction Toaster

Before using `txToaster()`, configure the global adapter in your app provider:

```tsx
import { setupTxToaster, SonnerAdapter } from 'typink';
import { toast } from 'sonner';

// Setup once at app initialization
setupTxToaster({
  adapter: new SonnerAdapter(toast),
  initialMessage: 'Signing transaction...',
  autoCloseDelay: 5000,
});
```

**Supported toast libraries:**

* **Sonner** (recommended) - `SonnerAdapter`
* **React-Toastify** - `ReactToastifyAdapter`
* **React-Hot-Toast** - `ReactHotToastAdapter`

See the txToaster documentation for more details.

### Complete Example

Here's a complete component showing query, transaction, and events:

```tsx
'use client';

import { useCallback, useState } from 'react';
import { useContract, useContractQuery, useContractTx, useWatchContractEvent, txToaster } from 'typink';
import { FlipperContractApi } from '@/contracts/types/flipper';

export function FlipperBoard() {
  const { contract } = useContract<FlipperContractApi>(ContractId.FLIPPER);
  const [events, setEvents] = useState<string[]>([]);

  // Query current value
  const { data: value, isLoading, refresh } = useContractQuery({
    contract,
    fn: 'get',
  });

  // Transaction to flip value
  const flipTx = useContractTx(contract, 'flip');

  // Watch for Flipped events
  useWatchContractEvent(
    contract,
    'Flipped',
    useCallback((newEvents) => {
      newEvents.forEach((event) => {
        setEvents((prev) => [...prev, `Flipped to: ${event.data.newValue}`]);
      });
    }, [])
  );

  const handleFlip = async () => {
    const toaster = txToaster('Flipping value...');

    try {
      await flipTx.signAndSend({
        callback: (result) => {
          toaster.onTxProgress(result);

          if (result.status.type === 'BestChainBlockIncluded' && !result.dispatchError) {
            refresh(); // Refresh query after success
          }
        },
      });
    } catch (error) {
      toaster.onTxError(error);
    }
  };

  return (
    <div>
      <h2>Flipper Contract</h2>

      {/* Display current value */}
      <div>
        <p>Current Value:</p>
        {isLoading ? (
          <span>Loading...</span>
        ) : (
          <span>{value?.toString()}</span>
        )}
      </div>

      {/* Flip button */}
      <button
        onClick={handleFlip}
        disabled={flipTx.inBestBlockProgress}>
        {flipTx.inBestBlockProgress ? 'Flipping...' : 'Flip Value'}
      </button>

      {/* Recent events */}
      <div>
        <h3>Recent Events:</h3>
        {events.map((event, i) => (
          <div key={i}>{event}</div>
        ))}
      </div>
    </div>
  );
}
```

**💡 This exact same pattern works for Solidity contracts!** Just change the contract type and method names.

***

**Happy building with Typink! 🎉**
