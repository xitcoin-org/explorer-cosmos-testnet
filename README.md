# Xitcoin Cosmos Testnet Explorer (superseded)

This repository contains the earlier standalone Cosmos explorer source. It is
retained for source history and is not the canonical deployment source.

The active public explorer is maintained in
[`xitcoin-org/explorer-testnet`](https://github.com/xitcoin-org/explorer-testnet).
Do not deploy this repository against the retired `xitcoin-testnet-1` network.

## Active network

| Property | Value |
| --- | --- |
| Network | Xitcoin Public Testnet |
| Cosmos chain ID | `xitcoin-testnet-v2-1` |
| Address prefix | `xtc` |
| Validator prefix | `xtcvaloper` |
| Native currency | XTC |
| Public explorer | https://explorer-testnet.xitcoin.org/ |

The retired `xitcoin-testnet-1` files exist only for historical verification.
Canonical network files are maintained in
[`xitcoin-org/testnets`](https://github.com/xitcoin-org/testnets), and node
software is maintained in
[`xitcoin-org/pos-chain`](https://github.com/xitcoin-org/pos-chain).

## Historical development

Requirements: Node.js and Yarn.

```bash
yarn install --frozen-lockfile
yarn dev
```

Create a production build:

```bash
yarn build
```

The historical network definition is stored in
`chains/testnet/xitcoin-testnet.json`. For current configuration and
deployment procedures, use
[`xitcoin-org/explorer-testnet`](https://github.com/xitcoin-org/explorer-testnet).

## Upstream

The interface is based on [ping-pub/explorer](https://github.com/ping-pub/explorer).
Xitcoin-specific current configuration and branding are maintained in the
canonical explorer repository.

## Security

Security reports follow [`SECURITY.md`](SECURITY.md).

## License

Distributed under the [GNU General Public License v2.0](LICENSE), consistent
with the upstream project.
