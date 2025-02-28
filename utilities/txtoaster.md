# txToaster

{% hint style="info" %}
WIP 🚧
{% endhint %}

### Quick look

`TODO` gif image here

### Usage

```tsx
// ...

import { useContract, useContractTx } from 'typink';
import { ContractId } from 'contracts/deployments.ts';
import { GreeterContractApi } from 'contracts/types/greeter';
import { txToaster } from '@/utils/txToaster.tsx';

const [message, setMessage] = useState('');
const { contract } = useContract<GreeterContractApi>(ContractId.GREETER);
const setMessageTx = useContractTx(contract, 'setMessage');

const doSetMessage = async () => {
  if (!contract || !message) return;

  const toaster = txToaster();
  
  try {
    await setMessageTx.signAndSend({
      args: [message],
      callback: (progress) => {
        const { status } = progress;
        console.log(status);

        if (status.type === 'BestChainBlockIncluded') {
          setMessage(''); // Reset the message if the transaction is in block
        }

        // showing a toast notifying transaction status
        toaster.onTxProgress(progress);
      },
    });
  } catch (e: any) {
    console.error('Fail to make transaction:', e);
    // showing a toast message
    toaster.onTxError(e);
  }
}

// ...
```



