# usePSP22Balance

The `usePSP22Balance` hook retrieves an account’s balance on a PSP22 contract. The hook also comes with a watch mode to continuously monitor balance changes, updating the value in real time.

### Usage

```tsx
import { usePSP22Balance, useTypink } from 'typink';

// ...
const { connectedAccount } = useTypink();
const psp22ContractAddress = '...'

const {
    data: myPsp22Balance,
    isLoading,
    refresh,
    // ...
  } = usePSP22Balance({
    contractAddress: psp22ContractAddress,
    address: connectedAccount?.address,
    watch: true
  });
  
// ...
```





