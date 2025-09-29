# useBlockInfo

Subscribes to best and finalized block updates in real time. Returns block number and hash for both best and finalized blocks with automatic updates.

#### Props

<table><thead><tr><th width="119.53125">Name</th><th width="203.671875">Type</th><th>Description</th></tr></thead><tbody><tr><td><code>options</code></td><td><code>NetworkOptions?</code></td><td>Optional network selection options (e.g., <code>{ networkId: 'polkadot' }</code>).</td></tr></tbody></table>

#### Return Type

<table><thead><tr><th width="138.27734375">Name</th><th width="224.19921875">Type</th><th>Description</th></tr></thead><tbody><tr><td><code>best</code></td><td><code>BlockInfo | undefined</code></td><td>Latest best block information (<code>{ number: number, hash: string }</code>).</td></tr><tr><td><code>finalized</code></td><td><code>BlockInfo | undefined</code></td><td>Latest finalized block information (<code>{ number: number, hash: string }</code>).</td></tr></tbody></table>

#### Basic Usage

**Display real-time block numbers:**

```tsx
import { useBlockInfo } from 'typink';

function BlockTracker() {
  const { best, finalized } = useBlockInfo();

  return (
    <div>
      <p>Best Block: #{best?.number}</p>
      <p>Finalized Block: #{finalized?.number}</p>
      <p>Best Hash: {best?.hash}</p>
    </div>
  );
}
```

**Multi-network block tracking:**

```tsx
function MultiNetworkBlocks() {
  const polkadotBlocks = useBlockInfo({ networkId: 'polkadot' });
  const kusamaBlocks = useBlockInfo({ networkId: 'kusama' });

  return (
    <div>
      <div>
        <h3>Polkadot</h3>
        <p>Best: #{polkadotBlocks.best?.number}</p>
        <p>Finalized: #{polkadotBlocks.finalized?.number}</p>
      </div>
      <div>
        <h3>Kusama</h3>
        <p>Best: #{kusamaBlocks.best?.number}</p>
        <p>Finalized: #{kusamaBlocks.finalized?.number}</p>
      </div>
    </div>
  );
}
```
