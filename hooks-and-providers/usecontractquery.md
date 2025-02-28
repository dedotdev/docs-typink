# useContractQuery

Making a query to a registered contract. This hook manages the state of a contract query, including loading state, query results, and error handling. It automatically fetches data when the contract, function, or arguments change.

<pre class="language-tsx"><code class="lang-tsx">// ...
import { useContract, useContractQuery } from 'typink';
import { ContractId } from 'contracts/deployments';
import { GreeterContractApi } from 'contracts/types/greeter';

const { contract } = useContract&#x3C;GreeterContractApi>(ContractId.GREETER);

<strong>const {
</strong>  data: greet,
  isLoading,
  refresh,
} = useContractQuery({
  contract,
  fn: 'greet',
});

// ...
</code></pre>

### Watch for changes

To automatically monitor updates in the contract storage, set the `watch` property to true.

```tsx
// ...

const {
  data: greet,
  isLoading,
  refresh,
  watch: true
} = useContractQuery({
  contract,
  fn: 'greet',
});

// ...
```



