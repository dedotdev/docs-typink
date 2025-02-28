# useWatchContractEvent

useWatchContractEvent makes it easy to listen for specific contract events on-chain. This is super handy when you want to trigger actions based on events—like showing a notification when the connected account receives funds via a transfer event.

Plus, with Dedot’s type system, all event parameters are fully typed, making it smoother than ever to work with contract events! 🚀

```tsx
// ...
const { contract } = useContract<GreeterContractApi>(ContractId.GREETER);

useWatchContractEvent(
  contract,
  'Greeted', // fully-typed event name with auto-completion
  useCallback((events) => {
    events.forEach((greetedEvent) => {
      const {
        name,
        data: { from, message },
      } = greetedEvent; // fully-typed events

      console.log(`Found a ${name} event sent from: ${from?.address()}, message: ${message}`);
    });
  }, []),
)
// ...
```

