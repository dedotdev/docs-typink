# useCheckMappedAccount

Checks if a Substrate account is mapped to an EVM address for ink! v6 contracts deployed on pallet-revive. This is essential for interacting with ink! v6 contracts on PolkaVM-based chains.

#### Props

| Name      | Type                | Description                                                                    |
| --------- | ------------------- | ------------------------------------------------------------------------------ |
| `address` | `SubstrateAddress?` | The Substrate address to check. Defaults to connected account if not provided. |
| `options` | `NetworkOptions?`   | Optional network selection options (e.g., `{ networkId: 'popTestnet' }`).      |

#### Return Type

| Name         | Type                   | Description                                                                                                   |
| ------------ | ---------------------- | ------------------------------------------------------------------------------------------------------------- |
| `isMapped`   | `boolean \| undefined` | Whether the account is mapped to an EVM address. `undefined` if not yet checked or revive pallet unavailable. |
| `isLoading`  | `boolean`              | Whether the mapping check is in progress.                                                                     |
| `error`      | `Error \| undefined`   | Any error that occurred during the check.                                                                     |
| `evmAddress` | `string \| undefined`  | The corresponding EVM address derived from the Substrate address.                                             |
| `refresh`    | `() => Promise<void>`  | Function to manually re-check the mapping status.                                                             |

#### Basic Usage

```tsx
import { useCheckMappedAccount } from 'typink';

function AccountMappingStatus() {
  const { isMapped, isLoading, evmAddress, refresh } = useCheckMappedAccount();

  if (isLoading) return <div>Checking mapping status...</div>;

  if (isMapped === false) {
    return (
      <div>
        <p>Account not mapped. Please map your account first.</p>
        <button onClick={refresh}>Check Again</button>
      </div>
    );
  }

  return <div>Account mapped to: {evmAddress}</div>;
}
```

**Check specific address:**

```tsx
const { isMapped, evmAddress } = useCheckMappedAccount(
  '5GrwvaEF5zXb26Fz9rcQpDWS57CtERHpNehXCPcNoHGKutQY',
  { networkId: 'popTestnet' }
);
```
