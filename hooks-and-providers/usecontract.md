# useContract

Initializing and managing Contract instance given a registered ContractId, it will use the information from contract deployments to initialize the instance.

```tsx
import { useContract } from 'typink';
import { ContractId } from 'contracts/deployments';
import { GreeterContractApi } from 'contracts/types/greeter';

const { contract } = useContract<GreeterContractApi>(ContractId.GREETER);

// contract.query...
```

