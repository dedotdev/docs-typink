# useDeployer & useDeployerTx

`useDeployer` and `useDeployerTx` are handy hooks for deploying smart contracts and managing transaction progress, just like `useContract` and `useContractTx`.

* `useDeployer`: Helps you create and manage a `ContractDeployer` instance using its unique ContractId from registered contract deployments.
* `useDeployerTx`: Works like `useContractTx`, letting you sign and send transactions to deploy a smart contract while keeping track of the progress.

These hooks make contract deployment smooth and hassle-free! 🚀

```tsx
// ...

import { useDeployer, useDeployerTx } from 'typink';
import { ContractId } from 'contracts/deployments';
import { greeterMetadata } from 'contracts/deployments.ts';

const wasm = greeterMetadata.source.wasm; // or greeterMetadata.source.hash (wasm hash code)
const { deployer } = useDeployer<GreeterContractApi>(greeterMetadata as any, wasm);
const newGreeterTx = useDeployerTx(deployer, 'new');
const [initMessage, setInitMessage] = useState<string>('');

const deployContraact = async () => {
  if (!contract || !initMessage) return;

  try {
    // a random salt to make sure we don't get into duplicated contract deployments issue
    const salt = numberToHex(Date.now()); 
    await newGreeterTx.signAndSend({
      args: [initMessage],
      txOptions: { salt },
      callback: ({ status }, deployedContractAddress) => {
        console.log(status);

        if (status.type === 'BestChainBlockIncluded') {
          setInitMessage('');
        }

        if (deployedContractAddress) {
          console.log('Contract is deployed at address', deployedContractAddress);
        }

        // TODO showing a toast notifying transaction status
      },
    });
  } catch (e: any) {
    console.error('Fail to make transaction:', e);
    // TODO showing a toast message
  }
}

// ...
```

