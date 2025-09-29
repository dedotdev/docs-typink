# useTxFee

Estimates transaction fees with automatic loading and error state management. Accepts a transaction created with `useTx` and automatically estimates fees based on the provided arguments.

#### Props

<table><thead><tr><th width="134.03515625">Name</th><th width="247.8671875">Type</th><th>Description</th></tr></thead><tbody><tr><td><code>tx</code></td><td><code>UseTxReturnType</code></td><td>Transaction instance from <code>useTx</code> hook.</td></tr><tr><td><code>args</code></td><td><code>any[]</code></td><td>Arguments for the transaction. Required if transaction has parameters.</td></tr><tr><td><code>txOptions</code></td><td><code>Partial&#x3C;PayloadOptions>?</code></td><td>Transaction options to include in fee estimation (e.g., tip, nonce).</td></tr><tr><td><code>enabled</code></td><td><code>boolean?</code></td><td>Whether to automatically fetch the fee estimate. Default: <code>true</code>.</td></tr><tr><td><code>networkId</code></td><td><code>NetworkId?</code></td><td>Optional network ID to specify which client to use.</td></tr></tbody></table>

#### Return Type

<table><thead><tr><th width="142.21875">Name</th><th width="205.1015625">Type</th><th>Description</th></tr></thead><tbody><tr><td><code>fee</code></td><td><code>bigint | null</code></td><td>The estimated fee in the smallest unit (e.g., Planck for Polkadot). <code>null</code> if not available.</td></tr><tr><td><code>isLoading</code></td><td><code>boolean</code></td><td>Whether fee estimation is in progress.</td></tr><tr><td><code>error</code></td><td><code>string | null</code></td><td>Error message if estimation failed, <code>null</code> otherwise.</td></tr><tr><td><code>refresh</code></td><td><code>() => Promise&#x3C;void></code></td><td>Function to manually trigger fee estimation.</td></tr></tbody></table>

#### Basic Usage

**Fee estimation with validation:**

```tsx
import { useTx, useTxFee, formatBalance } from 'typink';

function RemarkWithFee() {
  const [message, setMessage] = useState('Hello!');
  const remarkTx = useTx((tx) => tx.system.remark);

  const { fee, isLoading, error } = useTxFee({
    tx: remarkTx,
    args: [message],
    enabled: message.trim().length > 0,
  });

  return (
    <div>
      <input
        value={message}
        onChange={(e) => setMessage(e.target.value)}
        placeholder="Enter message"
      />

      {isLoading && <p>Calculating fee...</p>}
      {error && <p style={{ color: 'red' }}>Error: {error}</p>}
      {fee && <p>Estimated Fee: {formatBalance(fee)}</p>}

      <button onClick={() => remarkTx.signAndSend({ args: [message] })}>
        Send Remark
      </button>
    </div>
  );
}
```

**Conditional fee estimation:**

```tsx
function TransferWithFee() {
  const [recipient, setRecipient] = useState('');
  const [amount, setAmount] = useState('');

  const transferTx = useTx((tx) => tx.balances.transferKeepAlive);

  const isValidRecipient = recipient.length === 48;
  const parsedAmount = amount ? BigInt(amount) : 0n;

  const { fee, isLoading, error } = useTxFee({
    tx: transferTx,
    args: [recipient, parsedAmount],
    enabled: isValidRecipient && parsedAmount > 0n,
  });

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
        type="number"
      />

      {fee && (
        <div>
          <p>Estimated Fee: {formatBalance(fee)}</p>
          <p>Total: {formatBalance(fee + parsedAmount)}</p>
        </div>
      )}

      <button
        onClick={() => transferTx.signAndSend({ args: [recipient, parsedAmount] })}
        disabled={isLoading || transferTx.inProgress}>
        Transfer
      </button>
    </div>
  );
}
```
