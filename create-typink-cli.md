# create-typink CLI

The `create-typink` CLI tool is designed to help developers quickly scaffold a new ink! dApp project using Typink & Dedot. It allows for various customizations, including selecting a template, example contract, wallet connector, and network ...

<figure><img src=".gitbook/assets/image (21).png" alt=""><figcaption></figcaption></figure>

### Usage

```sh
npx create-typink@latest
```

### Command Syntax

```
npx create-typink<@version> [options]
```

### Options

| Option                        | Alias            | Description                                                                                                 |
| ----------------------------- | ---------------- | ----------------------------------------------------------------------------------------------------------- |
| `--name <project-name>`       | `-n`             | Specify the project name.                                                                                   |
| `--template <template-name>`  | `-t`             | Choose a template (default: `base`).                                                                        |
| `--preset <preset-contract>`  | `-p`             | Choose an example preset contract (`base`, `psp22`, `greeter`, `none`).                                     |
| `--wallet <wallet-connector>` | `-w`             | Choose a wallet connector (`Default`, `Subconnect`, `Talisman Connect`).                                    |
| `--network <network-name>`    | `-N`             | Choose a network (`Pop Testnet`, `Aleph Zero Testnet`, `Aleph Zero Mainnet`, `Astar`, `Shiden`, `Shibuya`). |
| `--skip`                      | `--skip-install` | Skip package installation.                                                                                  |
| `--no-git`                    |                  | Skip git initialization.                                                                                    |
| `--version`                   | `-v`             | Show Typink version.                                                                                        |
| `--help`                      | `-h`             | Show help.                                                                                                  |

### Notes

* The CLI provides an interactive experience if options are not specified.
* The `--skip-install` flag is useful when you want to install dependencies manually.
* The `--no-git` flag prevents automatic git initialization.
