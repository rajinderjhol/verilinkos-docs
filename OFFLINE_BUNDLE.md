# Offline verification bundle

Last reviewed: 2026-11-22

## What it is

A signed verification bundle is a single ZIP that lets a third party verify a
VAP receipt **fully offline**, with no account, no network and no trust in the
VeriLinkOS platform. It contains:

| Path | Purpose |
|---|---|
| `receipt.json` | The signed VAP receipt |
| `registry/trusted_signers.json` | Signed trusted-signer registry, including the revocation list |
| `keys/root_public_key.txt` | Hex Ed25519 root public key that signs the registry and manifest |
| `offline_vap_verifier.py`, `verifier/verify.py`, `core_verification/*.py` | Self-contained verifier sources |
| `MANIFEST.json` | SHA-256 of every file plus an Ed25519 signature over the manifest |

**No private keys are included.** The root public key must still be obtained
from a trusted out-of-band source. The bundle proves integrity and makes the
verification portable; it does not by itself prove who owns that root key.

## Build

From the VeriLinkOS repository (the root private key stays in local `.env` and
is never written into the bundle):

```bash
set -a; source .env; set +a
.venv/bin/python scripts/build_verification_bundle.py \
  --receipt /path/to/receipt.json \
  --registry "$VERILINK_SIGNER_REGISTRY" \
  --root-key-private "$VERILINK_REGISTRY_ROOT_PRIVATE_KEY" \
  --out /path/to/vap-bundle.zip
```

## Verify

```bash
.venv/bin/python scripts/verify_verification_bundle.py --bundle /path/to/vap-bundle.zip
```

The tool verifies the manifest signature and every file hash, then runs the
**bundled** verifier against the bundled registry with trust required. Exit `0`
means the receipt is `VALID`; exit `7` means bundle integrity failed; other exit
codes come from the verifier (`2` signature, `3` delegation/hop, `4` schema,
`5` untrusted/revoked, `6` trust required but not verified).

A tampered receipt, registry, root key or verifier source is reported as a
bundle integrity failure before any verdict is printed.

## Verifier parity

All VAP verifier surfaces now call one shared engine,
`core_verification/offline.py`:

- runtime verifier (`verilink/vap_utils.py`) — same registry, revocation and
  per-hop signature primitives;
- clean-room CLI (`offline_vap_verifier.py`);
- standalone auditor (`verifier/verify.py`).

`tests/test_offline_verifier_parity.py` feeds identical receipts to every
surface and asserts identical verdicts for valid, tampered, unregistered,
revoked and legacy-hop receipts. `tests/test_verification_bundle.py` covers
bundle integrity and confirms no private key is embedded.

## Honest status reporting

Checks are reported as `pass`, `fail` or `skip` — never silently passed.
Without a registry bundle, cryptographic integrity can still be checked, but
the verdict is `INTEGRITY_ONLY`, not `VALID`. `--require-trust` (or the bundle
verifier, which always requires it) refuses that weaker verdict. Timestamp
verification is `skip` unless a verifier is supplied; DQI is advisory and is
not part of any verdict.
