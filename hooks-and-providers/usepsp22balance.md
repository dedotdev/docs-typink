# usePSP22Balance

{% hint style="info" %}
WIP  🚧
{% endhint %}

### Usage

```tsx
import { usePSP22Balance, useContract, useTypink } from 'typink';
import { ContractId } from 'contracts/deployments';
import { GreeterContractApi } from 'contracts/types/greeter';

// ...
const { connectedAccount } = useTypink();
const { contract } = useContract<GreeterContractApi>(ContractId.GREETER);

const {
    data: myPsp22Balance,
    isLoading,
    refresh,
    // ...
  } = usePSP22Balance({
    contractAddress: contract?.address?.address(),
    address: connectedAccount?.address,
    watch: true
  });
  
// ...
```





