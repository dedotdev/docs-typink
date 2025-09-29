# useTx

Creates and manages general Substrate transactions with improved type safety. Provides functions to sign and send transactions, estimate fees, and track transaction progress.

#### Props

<table><thead><tr><th width="132.421875">Name</th><th>Type</th><th>Description</th></tr></thead><tbody><tr><td><code>txBuilder</code></td><td><code>(tx) => TxFunction</code></td><td>Callback function that receives the <code>tx</code> object and returns a transaction method (e.g., <code>(tx) => tx.system.remark</code>).</td></tr><tr><td><code>options</code></td><td><code>NetworkOptions?</code></td><td>Optional network selection options (e.g., <code>{ networkId: 'polkadot' }</code>).</td></tr></tbody></table>

#### Return Type

<table><thead><tr><th width="206.578125">Name</th><th width="209.71875">Type</th><th>Description</th></tr></thead><tbody><tr><td><code>signAndSend</code></td><td><code>(params?) => Promise&#x3C;void></code></td><td>Function to sign and send the transaction. Accepts <code>{ args?, txOptions?, callback? }</code>.</td></tr><tr><td><code>getEstimatedFee</code></td><td><code>(params?) => Promise&#x3C;bigint></code></td><td>Function to estimate the transaction fee. Accepts <code>{ args?, txOptions? }</code>.</td></tr><tr><td><code>inProgress</code></td><td><code>boolean</code></td><td>Whether a transaction is currently in progress (from signing to finalization).</td></tr><tr><td><code>inBestBlockProgress</code></td><td><code>boolean</code></td><td>Whether the transaction has been included in the best block.</td></tr></tbody></table>

#### Basic Usage

**System remark transaction:**

```tsx
import { useTx } from 'typink';

function RemarkExample() {
  const remarkTx = useTx((tx) => tx.system.remark);

  const handleSend = async () => {
    await remarkTx.signAndSend({
      args: ['Hello from Typink!'],
      callback: (result) => {
        console.log('Transaction status:', result.status.type);
      },
    });
  };

  return (
    <button
      onClick={handleSend}
      disabled={remarkTx.inBestBlockProgress}>
      {remarkTx.inBestBlockProgress ? 'Sending...' : 'Send Remark'}
    </button>
  );
}
```

**Balance transfer transaction:**

```tsx
function TransferExample() {
  const transferTx = useTx((tx) => tx.balances.transferKeepAlive);

  const handleTransfer = async () => {
    const recipient = '5GrwvaEF5zXb26Fz9rcQpDWS57CtERHpNehXCPcNoHGKutQY';
    const amount = 1000000000000n; // 1 DOT (10 decimals)

    await transferTx.signAndSend({
      args: [recipient, amount],
      callback: (result) => {
        if (result.status.type === 'BestChainBlockIncluded') {
          console.log('Transfer included in best block');
        }
        if (result.status.type === 'Finalized') {
          console.log('Transfer finalized');
        }
      },
    });
  };

  return (
    <button
      onClick={handleTransfer}
      disabled={transferTx.inProgress}>
      Transfer
    </button>
  );
}
```
