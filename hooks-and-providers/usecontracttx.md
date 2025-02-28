# useContractTx

The `useContractTx` hook makes it easy to send transactions to a smart contract while keeping track of their progress.&#x20;

Before actually sending a transaction, it runs a dry run to catch any potential issues or errors, so you don’t waste time on failed attempts. Once everything looks good, it signs and submits the transaction, updating you on its status every step of the way.

{% hint style="info" %}
Don’t forget to use the built-in [txToaster](https://docs.dedot.dev/typink/utilities/txtoaster) to display notifications about the transaction’s progress, so users stay informed in real-time! 🚀
{% endhint %}

```tsx
// ...
import { useContract, useContractTx } from 'typink';
import { ContractId } from 'contracts/deployments';
import { GreeterContractApi } from 'contracts/types/greeter';

const [message, setMessage] = useState('');
const { contract } = useContract<GreeterContractApi>(ContractId.GREETER);
const setMessageTx = useContractTx(contract, 'setMessage');

const doSetMessage = async () => {
  if (!contract || !message) return;

  try {
    await setMessageTx.signAndSend({
      args: [message],
      callback: ({ status }) => {
        console.log(status);

        if (status.type === 'BestChainBlockIncluded') {
          setMessage(''); // Reset the message if the transaction is in block
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

### UI Feedback During Transaction Processing

You’ll often want to disable UI (buttons or form inputs) after a user clicks Sign & Send to prevent duplicate submissions or show transaction progress.&#x20;

The useContractTx hook provides handy boolean states to make this easy and convenient for you!

```tsx
// ...
const setMessageTx = useContractTx(contract, 'setMessage');

// ... loading until the transaction is in best blocks
<Button type='submit' size='sm' mt={4} isLoading={setMessageTx.inBestBlockProgress}>
  Sign & Send
</Button>

// OR ... loading until the transaction is finalized
<Button type='submit' size='sm' mt={4} isLoading={setMessageTx.inProgress}>
  Sign & Send
</Button>
// ...
```

