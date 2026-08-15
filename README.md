# Xitcoin Cosmos Testnet Explorer

Source and configuration for the Xitcoin Cosmos testnet explorer.

## Network

| Property | Value |
| --- | --- |
| Network | Xitcoin Testnet |
| Cosmos chain ID | `xitcoin-testnet-2026-1` |
| Address prefix | `xtc` |
| Validator prefix | `xtcvaloper` |
| Native currency | XTC |
| Public explorer | https://explorer-testnet.xitcoin.org/ |

The deployed network identifier remains documented here until the next testnet genesis is activated. Repository naming and public navigation use the stable name **Xitcoin Testnet**.

## Development

Requirements: Node.js and Yarn.

```bash
yarn install --frozen-lockfile
yarn dev
```

Create a production build:

```bash
yarn build
```

## Configuration

The Xitcoin network definition is stored in `chains/testnet/xitcoin-testnet.json`. Public RPC, API and gRPC endpoints must use HTTPS or TLS and must match the active testnet.

Official token artwork comes from [`xitcoin-org/brand`](https://github.com/xitcoin-org/brand). Explorer-specific assets must preserve the standalone XTC symbol.

## Upstream

The interface is based on [ping-pub/explorer](https://github.com/ping-pub/explorer). Xitcoin-specific configuration, branding and deployment automation are maintained in this repository.
