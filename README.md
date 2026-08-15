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

Canonical network identity and reset-candidate documentation are maintained in [`xitcoin-org/pos-chain`](https://github.com/xitcoin-org/pos-chain). Official token artwork comes from [`xitcoin-org/brand`](https://github.com/xitcoin-org/brand).

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

## Upstream

The interface is based on [ping-pub/explorer](https://github.com/ping-pub/explorer). Xitcoin-specific configuration, branding and deployment automation are maintained in this repository.

## Security

Security reports follow [`SECURITY.md`](SECURITY.md).

## License

Distributed under the [GNU General Public License v2.0](LICENSE), consistent with the upstream project.
