# Anchoring Evidence — Polygon Amoy

**Date:** 2026-11-21
**Status:** real on-chain transaction, confirmed and verified

This document records a live anchoring test of the VeriLinkOS reference
implementation. VeriLinkOS has **two anchoring layers**:

1. **Local / offline anchor (always produced).** Every receipt is bound to a
   real Merkle root and an offline anchor record with a deterministic tx hash
   and block derived from that root. This is decision evidence and works in
   air-gapped or development environments; the chain label `Polygon (Simulated)`
   honestly means "no public chain was contacted", not "no anchor exists".
2. **Public / private chain anchor (optional).** When `BLOCKCHAIN_MODE` selects
   a real chain and an RPC/key are configured, the Merkle root is broadcast
   with real `web3` code (`AsyncWeb3`, EIP-1559 fees, `send_raw_transaction`)
   and verified on-chain. Public blockchains are optional for specific use
   cases — a hospital network, for example, may use a private ledger instead.

It is disabled in development to avoid spending test tokens; for the test
below, the mode was set to `POLYGON_AMOY`.

## Transaction

| Field | Value |
|---|---|
| Network | Polygon Amoy (testnet) |
| Chain ID | 80002 |
| RPC | `https://polygon-amoy-bor-rpc.publicnode.com` |
| Mode | `POLYGON_AMOY` (real chain, not simulated) |
| Contract | none configured — transaction self-anchors to the wallet address |
| Merkle root | `386e50bdf913072d16cea98c572cb76d07b61131ef72c488cdcb531ecb6c1b0e` |
| Transaction hash | `0xe6aca84d29f49a8019a20e640c12bd4aaf8b0003df3f9c7f913dbec4791c58df` |
| Confirmed block | 53467317 |
| On-chain verification | passed (`verify_anchor_on_chain` found the root in tx input data) |

Explorer: https://amoy.polygonscan.com/tx/0xe6aca84d29f49a8019a20e640c12bd4aaf8b0003df3f9c7f913dbec4791c58df

## Canonical payload anchored

```json
{"date":"2026-11-21","product":"VeriLinkOS","purpose":"anchoring evidence test","spec":"VAP merkle root broadcast to Polygon Amoy"}
```

The Merkle root is `SHA-256` of that canonical JSON (sorted keys, no spaces).

## How it was produced

`BlockchainService.anchor_receipt(merkle_root, action_type)` in
`verilink/blockchain.py`:

1. Reads `BLOCKCHAIN_MODE=POLYGON_AMOY`, RPC URL and chain ID from the environment.
2. Loads `ANCHOR_PRIVATE_KEY` through the KMS layer and derives the wallet.
3. Builds an EIP-1559 transaction with the Merkle root in the `data` field.
4. Signs and broadcasts it with `send_raw_transaction`.
5. Waits for the receipt and records the real `blockNumber` (this confirmation
   step was added on 2026-11-21; earlier anchors recorded `block = 0`).
6. `verify_anchor_on_chain(tx_hash, expected_root)` queries the transaction and
   confirms the root is present in the input data.

## What this proves and what it does not

- **Proves:** the real anchoring path broadcasts a transaction to Polygon Amoy,
  obtains a confirmation, records the block, and can verify the root from the
  chain.
- **Does not prove:** mainnet anchoring, a dedicated anchor contract, or a
  third-party conformance certification. No `ANCHOR_CONTRACT_ADDRESS` was
  configured, so the transaction was self-addressed.
- Development defaults remain `BLOCKCHAIN_MODE=SIMULATED`: the local/offline
  anchor is still real, but no public chain is contacted and the anchor is
  labelled `"Polygon (Simulated)"`. It never claims an on-chain transaction.
- The public-chain leg is optional and deployment-specific; private ledgers
  are a supported direction for regulated networks.
