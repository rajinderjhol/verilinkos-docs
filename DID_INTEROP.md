# DID interoperability boundary

Last reviewed: 2026-11-22

## Current state (shipped)

VeriLinkOS resolves two DID methods, both local to the deployment:

| Method | Format | Resolved by | Backing store |
|---|---|---|---|
| `did:mesh` | `did:mesh:<sha256-of-public-key>` | `MeshDIDRegistry` / `DIDResolver` | Local agent registry + DB |
| `did:human` | `did:human:<uuid>` | `HumanDIDRegistry` / `DIDResolver` | Local user table |

Resolution is **offline and fail-closed**. An unregistered DID yields no
document; the resolver never fabricates a DID Document from the DID string
itself (the `did:mesh` suffix is a SHA-256 fingerprint, not an Ed25519 key, so
treating it as `publicKeyHex` would invent a false identity).

The boundary is enforced in code, not just described:
`verilink.dids.is_supported_did()` returns `True` only for `mesh` and `human`.
`did:key`, `did:web`, and any unknown method are reported unsupported and
produce no DID Document. See `tests/test_dids.py::test_did_interop_boundary`.

## Not supported (explicitly out of scope)

| Method | Why not |
|---|---|
| `did:key` | Decoding multibase key material and verifying its concrete verification-method suite is a separate trust model. Receipt verification already binds Ed25519 public keys directly; adding a key decoder would introduce a second, untested source of identity. |
| `did:web` | Requires HTTP/DNS resolution, a TLS trust decision, and a domain-control assumption — all network dependencies that conflict with the offline-verification promise. |
| Other methods | No resolver exists; unknown methods fail closed. |

We do **not** claim W3C DID interoperability certification. The DID Documents we
emit use W3C-compatible shapes (`@context`, `verificationMethod`,
`authentication`, `assertionMethod`), but emitting a compatible document is not
the same as passing a W3C interop test suite. That claim remains a separate
decision requiring an external test.

## Interop without a resolver: portable public keys

Third parties do not need a DID resolver to verify a receipt. The signed
receipt carries the Ed25519 public key and signature directly; the offline
verifier checks the signature against the key and checks that key against the
signed trusted-signer registry (`verilink/trusted_signers.json`). This is the
supported interop path. DIDs are a local indexing convenience, not a dependency
of verification.

## What would change this boundary

Adding `did:key` or `did:web` would require, at minimum:
1. A real decoder/resolver with its own conformance tests.
2. A documented trust model for method-specific resolution.
3. Updated conformance vectors and an explicit scope decision.

Until that work exists and is documented, these methods stay unsupported and
fail closed.
