# useTypink

Access various shared states via `useTypink`

```tsx
// ...
const { 
  accounts, // list available accounts connected from the wallet
  connectedAccount, // connected account to interact with contracts & networks
  network, // current connected network info
  client, // Dedot clients to interact with network
  deployments, // contract deployments
  connectedWallet, // connected wallet
  connectWallet, // func to connect to a wallet given its id
  disconnect, // func to sign out and disconnect from the wallet
  wallets, // available wallets
  ...
} = useTypink();
// ...
```
