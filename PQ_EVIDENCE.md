# Post-Quantum Signature Evidence

**Date:** 2026-11-21
**Status:** real ML-DSA-87 signatures produced and verified offline

## What was configured

- Installed `liboqs-python>=0.16.0.2` (the PyPI package `oqs` is an unrelated
  expression interpreter). Native `liboqs` footprint is about 9.3 MB.
- Generated a real ML-DSA-87 keypair and stored it in the deployment `.env` as
  `PQ_PRIVATE_KEY` / `PQ_PUBLIC_KEY`. Values are not published.
- Enabled `features.quantum_enabled` for the root organization via
  `/organization`.

## Observed receipt

Generated through the real HITL flow (`/v1/policy/authorize` → `/v1/policy/approve`):

| Field | Observed value |
|---|---|
| Receipt ID | `VAP-20261121-GENBYL-9545` |
| Receipt format version | `3.0` |
| `proof.signature_algorithm` | `Ed25519+ML-DSA-87` |
| `proof.signature` (Ed25519) | 128 hex chars |
| `proof.pq_signature` | 9254 hex chars (4627 bytes) |
| `proof.pq_signer_public_key` | 5184 hex chars (2592 bytes) |
| `human_principal_did` | `did:human:ciso` |

A direct `/v1/vap/receipt/generate` call produced `VAP-20261121-AIDU7H-BF3C`
with the same dual-signature fields.

## Offline verification result

`verify_receipt` through the MCP tool reported:

- Step 1 schema: valid
- Step 2 Ed25519: verified
- Step 2 ML-DSA-87: **verified**
- Step 2 dual signature: **verified**
- Step 3 trusted signer: passed
- Step 4 Merkle path: verified
- Step 6 policy active: passed
- Step 7 human approval: **not satisfied** — the approval workflow recorded a
  `human_principal_did` but no human signature, so the verifier correctly marks
  the receipt invalid.

## What this proves and what remains

**Proves:** the org preference controls real ML-DSA-87 signing; the receipt
carries both signatures; and the offline verifier checks both.

**Remains:** the HITL approval flow does not yet carry a real human signature,
so autonomy-restricted receipts still fail verification step 7. That is a
separate gap from post-quantum signing and is reported honestly rather than
papered over with a fabricated signature.
