# Silo V2 lending vaults on Sonic

This original ATQ module retrieves legacy Silo Finance V2 lending-market vault
contracts on Sonic (EVM chain 146). Each returned market contract is also its
ERC-20 deposit-share token. The category is the `Market` entity, rather than
protected-collateral tokens, debt tokens or aggregate managed vaults.

## Specific source documentation

Silo Finance's own [snapshot documentation](https://github.com/silo-finance/lenders-snapshot/blob/58dbfc612906307137c305d56adf9f1a7ea61dce/scripts/lender-snapshot/README.md)
describes the `subgraph_url` configuration and identifies `snapshot_lenders.py`
as its canonical configuration. That [documented configuration](https://github.com/silo-finance/lenders-snapshot/blob/58dbfc612906307137c305d56adf9f1a7ea61dce/scripts/lender-snapshot/snapshot_lenders.py)
explicitly selects Sonic's subgraph
`8wcbzcdNirQvk1ETh25wpVzb5GWs8DvugpbwrYnTCcxj`. This is a reference to that
specific subgraph, without inferring authorization for other publications by
the same address. The project also identifies these chain endpoints as legacy
V2 sources in its [borrower tooling](https://github.com/silo-finance/actions/blob/0e0a4f79772a176846e181483d1f22adf5b26948/scripts/pull_borrowers/fetch_legacy_borrowers.py).

The [named subgraph](https://thegraph.com/explorer/subgraphs/8wcbzcdNirQvk1ETh25wpVzb5GWs8DvugpbwrYnTCcxj?chain=arbitrum-one&view=About)
resolved on 2026-10-28 to deployment
`QmQUcPAEpUQJL3AYK21hsgNnj6XmWepTXJXySHdFXMmp7p`. The module queries that
explicit deployment through The Graph gateway, with the supplied key only in
the authorization header. Recheck the named source before filing an item.

## Retrieval and naming

All market rows are read at one block hash with an unfiltered first page,
strict ascending unique-ID cursor pages and a final metadata seal. Read errors,
indexing errors, changed anchors, malformed vault identities and unfinished
pagination throw. Every returned market must identify itself as its `sToken`.
The page bound throws rather than returning partial output.

Names combine the source's actual `siloId` and published market name. Missing
or invalid name metadata uses the source's market index. Long names use the
policy's ellipsis convention. Name collisions use the full contract address;
no market is omitted and no new instance number is invented. Notes identify
the asset, Silo configuration, paired lending vault and source market index.

The UI link is the protocol application. Identifying a legacy contract does
not indicate that the application currently offers that particular market.

## Build

Use Yarn if installing dependencies, then run `yarn build`. All retrieval and
tag construction logic is in `src/main.mts`; it exports only the asynchronous
two-argument `returnTags` function. The module was authored from scratch and is
MIT licensed.

All source market instances are retained, including zero-asset configurations. A zero-asset instance receives its source Silo ID and market index as its name, and its note identifies it as an ERC-20 share token and says that no underlying asset is identified. It is not presented as a usable deposit receipt for a named asset. No row is removed for this condition. Source market indices are restricted to the documented two-vault configuration (0 or 1).
