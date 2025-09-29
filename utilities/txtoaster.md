---
description: Showing transaction notifications with real-time progress updates
---

# txToaster

The `txToaster` utility provides a generic, adapter-based system for displaying transaction notifications with real-time progress updates. It supports any toast notification library through adapters, keeping users informed about transaction status, block inclusion, and errors.

### Quick look

<figure><img src="../.gitbook/assets/txToaster.gif" alt=""><figcaption></figcaption></figure>

### Quick Start

#### 1. Install a Toast Library

Choose and install one of the supported toast libraries:

```bash
# Sonner (recommended)
npm install sonner

# React-Toastify
npm install react-toastify

# React-Hot-Toast
npm install react-hot-toast
```

#### 2. Setup the Adapter

Configure the global adapter once in your app's entry point:

```tsx
import { setupTxToaster, SonnerAdapter } from 'typink';
import { toast } from 'sonner';

// Setup once at app initialization
setupTxToaster({
  adapter: new SonnerAdapter(toast),
});
```

#### 3. Use in Components

Create toaster instances for each transaction:

```tsx
import { txToaster } from 'typink';

const toaster = txToaster('Signing transaction...');

try {
  await tx.signAndSend({
    args: [/* ... */],
    callback: (result) => {
      toaster.onTxProgress(result);
    },
  });
} catch (error) {
  toaster.onTxError(error);
}
```

### Built-in Adapters

Typink provides three built-in adapters for popular toast libraries:

#### SonnerAdapter

For [Sonner](https://sonner.emilkowal.ski/) toast library (recommended for its simplicity and beautiful default styling).

```tsx
import { setupTxToaster, SonnerAdapter } from 'typink';
import { toast } from 'sonner';
import { Toaster } from 'sonner';

setupTxToaster({
  adapter: new SonnerAdapter(toast),
});

// Add Toaster component to your app
function App() {
  return (
    <>
      <Toaster position="top-right" />
      {/* Your app content */}
    </>
  );
}
```

#### ReactToastifyAdapter

For [React-Toastify](https://fkhadra.github.io/react-toastify/) library.

```tsx
import { setupTxToaster, ReactToastifyAdapter } from 'typink';
import { toast, ToastContainer } from 'react-toastify';
import 'react-toastify/dist/ReactToastify.css';

setupTxToaster({
  adapter: new ReactToastifyAdapter(toast),
});

// Add ToastContainer component to your app
function App() {
  return (
    <>
      <ToastContainer position="top-right" />
      {/* Your app content */}
    </>
  );
}
```

#### ReactHotToastAdapter

For [React-Hot-Toast](https://react-hot-toast.com/) library.

```tsx
import { setupTxToaster, ReactHotToastAdapter } from 'typink';
import { toast, Toaster } from 'react-hot-toast';

setupTxToaster({
  adapter: new ReactHotToastAdapter(toast),
});

// Add Toaster component to your app
function App() {
  return (
    <>
      <Toaster position="top-right" />
      {/* Your app content */}
    </>
  );
}
```

### Complete Usage Examples

#### Contract Transaction

```tsx
import { useContract, useContractTx, txToaster } from 'typink';
import { GreeterContractApi } from './types/greeter';

function GreeterComponent() {
  const [message, setMessage] = useState('');
  const { contract } = useContract<GreeterContractApi>('greeter');
  const setMessageTx = useContractTx(contract, 'setMessage');

  const handleSetMessage = async () => {
    if (!message) return;

    const toaster = txToaster('Setting message...');

    try {
      await setMessageTx.signAndSend({
        args: [message],
        callback: (progress) => {
          // Update toast with transaction progress
          toaster.onTxProgress(progress);

          // Reset input on success
          if (progress.status.type === 'Finalized' && !progress.dispatchError) {
            setMessage('');
          }
        },
      });
    } catch (error) {
      console.error('Transaction failed:', error);
      toaster.onTxError(error);
    }
  };

  return (
    <div>
      <input
        value={message}
        onChange={(e) => setMessage(e.target.value)}
        placeholder="Enter message"
      />
      <button
        onClick={handleSetMessage}
        disabled={setMessageTx.inProgress}>
        {setMessageTx.inProgress ? 'Setting...' : 'Set Message'}
      </button>
    </div>
  );
}
```

#### General Substrate Transaction

```tsx
import { useTx, txToaster } from 'typink';

function TransferComponent() {
  const [recipient, setRecipient] = useState('');
  const [amount, setAmount] = useState('');
  const transferTx = useTx((tx) => tx.balances.transferKeepAlive);

  const handleTransfer = async () => {
    const toaster = txToaster('Transferring tokens...');

    try {
      await transferTx.signAndSend({
        args: [recipient, BigInt(amount)],
        callback: (result) => {
          toaster.onTxProgress(result);

          const { status, dispatchError } = result;

          if (status.type === 'BestChainBlockIncluded') {
            if (!dispatchError) {
              console.log('Transfer included in block!');
            } else {
              console.error('Transfer failed in block:', dispatchError);
            }
          }

          if (status.type === 'Finalized') {
            if (!dispatchError) {
              setRecipient('');
              setAmount('');
            }
          }
        },
      });
    } catch (error) {
      toaster.onTxError(error);
    }
  };

  return (
    <div>
      <input
        value={recipient}
        onChange={(e) => setRecipient(e.target.value)}
        placeholder="Recipient address"
      />
      <input
        value={amount}
        onChange={(e) => setAmount(e.target.value)}
        placeholder="Amount"
      />
      <button onClick={handleTransfer}>Transfer</button>
    </div>
  );
}
```

#### With Custom Messages

```tsx
const handleMint = async () => {
  const toaster = txToaster({
    initialMessage: 'Preparing to mint tokens...',
    messages: {
      inProgress: '⏳ Minting tokens...',
      successful: '✅ Tokens minted successfully!',
      failed: '❌ Minting failed',
    },
    autoCloseDelay: 10000,
  });

  try {
    await mintTx.signAndSend({
      args: [amount],
      callback: (progress) => {
        toaster.onTxProgress(progress);

        // Custom logic based on status
        if (progress.status.type === 'BestChainBlockIncluded') {
          console.log('Tokens minted in block:', progress.status.value.blockNumber);
        }
      },
    });
  } catch (error) {
    toaster.onTxError(error);
  }
};
```

