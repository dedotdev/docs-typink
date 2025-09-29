# create-typink CLI

## create-typink CLI

The `create-typink` CLI tool helps developers quickly scaffold a new ink! or Solidity dApp project using Typink & Dedot. It provides an interactive experience to select contract types, templates, and networks, automatically setting up a complete Next.js application with pre-deployed example contracts.

### Quick Start

```bash
# npm
npx create-typink@latest

# pnpm
pnpm create typink@latest

# yarn
yarn create typink@latest

# bun
bunx create-typink@latest
```

The CLI will guide you through an interactive setup process to configure your project.

### Contract Types & Templates

create-typink supports four contract types across two different pallets:

| Contract Type         | Pallet           | Technology | Template           | Example Contract |
| --------------------- | ---------------- | ---------- | ------------------ | ---------------- |
| **Ink! v6**           | pallet-revive    | PolkaVM    | `inkv6-nextjs`     | Flipper          |
| **Ink! v6 (Sol ABI)** | pallet-revive    | PolkaVM    | `inkv6-sol-nextjs` | Flipper          |
| **Solidity**          | pallet-revive    | PolkaVM    | `sol-nextjs`       | Storage          |
| **Ink! v5**           | pallet-contracts | WASM       | `inkv5-nextjs`     | Greeter          |

#### Template Features

All templates are built with Next.js and include:

* **TypinkProvider** configuration with wallet connector
* **Pre-deployed example contract** with working interactions
* **TypeScript bindings** for the example contract
* **UI components** demonstrating contract queries and transactions
* **Ready-to-use setup** for immediate development

### Options

<table><thead><tr><th width="238.16015625">Option</th><th width="127.62890625">Alias</th><th>Type</th><th>Description</th></tr></thead><tbody><tr><td><code>--name &#x3C;project-name></code></td><td><code>-n</code></td><td>String</td><td>Specify the project name. Must be a valid npm package name.</td></tr><tr><td><code>--template &#x3C;template-name></code></td><td><code>-t</code></td><td>String</td><td>Choose a template: <code>inkv6-nextjs</code>, <code>inkv6-sol-nextjs</code>, <code>sol-nextjs</code>, <code>inkv5-nextjs</code>.</td></tr><tr><td><code>--networks &#x3C;network-names></code></td><td><code>-N</code></td><td>String[]</td><td>Select supported networks (multiple values allowed). Networks must match the template's pallet type.</td></tr><tr><td><code>--skip-install</code></td><td><code>--skip</code></td><td>Boolean</td><td>Skip automatic package installation.</td></tr><tr><td><code>--no-git</code></td><td></td><td>Boolean</td><td>Skip git initialization.</td></tr><tr><td><code>--help</code></td><td><code>-h</code></td><td>Boolean</td><td>Show help message.</td></tr></tbody></table>

#### Notes

* **Project Name Validation**: The project name must be a valid npm package name (lowercase, no spaces, etc.)
* **Network Compatibility**: Selected networks must be compatible with the chosen template:
  * `inkv5-nextjs` → pallet-contracts networks only
  * `inkv6-nextjs`, `inkv6-sol-nextjs`, `sol-nextjs` → pallet-revive networks only
* **Multiple Networks**: You can select multiple networks using multiple `-N` flags or by selecting multiple in interactive mode

### Interactive Mode

When you run `npx create-typink@latest` without options, the CLI guides you through an interactive setup:

#### 1. Project Name

```
? Project name: (my-typink-app)
```

Enter a valid npm package name for your project.

#### 2. Contract Type Selection

```
? Select contract type:
❯ Ink! v6 (PolkaVM, pallet-revive)
  Ink! v6 using Sol ABI (PolkaVM, pallet-revive)
  Solidity (PolkaVM, pallet-revive)
  Ink! v5 (WASM, pallet-contracts)
```

Choose the contract type you want to work with. This determines which template and networks are available.

#### 3. Network Selection

```
? Select supported networks: (Press <space> to select, <a> to toggle all)
❯◉ Pop Testnet
 ◯ Aleph Zero Testnet
 ◯ Aleph Zero
 ◯ Astar
```

Select one or more networks for your dApp. Available networks depend on the chosen contract type.

### Generated Project Structure

After running the CLI, your project will have the following structure:

```
my-typink-app/
├── src/
│   ├── app/                   # Next.js app directory
│   ├── components/            # React components
│   │   ├── shared/           # Shared UI components
│   │   └── [example]-board.tsx  # Example contract interactions
│   ├── contracts/            # Contract-related files
│   │   ├── deployments.ts    # Contract deployment addresses
│   │   └── types/           # Generated TypeScript bindings
│   ├── lib/                  # Utility functions
│   └── providers/            # React context providers
├── public/                   # Static assets
├── .gitignore
├── package.json
├── tsconfig.json
├── next.config.ts
└── README.md
```

#### Key Files

* **`src/contracts/deployments.ts`**: Contains contract deployment information for selected networks
* **`src/contracts/types/`**: TypeScript bindings for type-safe contract interactions
* **`src/components/`**: Example components showing how to interact with contracts
* **`src/providers/`**: TypinkProvider configuration with wallet connector setup
