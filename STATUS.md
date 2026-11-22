# Implementation Status Addendum — 2026-11-22

> This page records what the companion VeriLinkOS code repository actually
> implements today. Where this page conflicts with older whitepapers or blog
> posts, **this page wins**. All claims here are about local implementation,
> not external certification. PersonaVault is a separate repository with its
> own status; no interoperation between the two systems is defined or claimed.

## Verification baseline

- Reference implementation: `github.com/rajinderjhol/VeriLinkOS`
- Full test suite: **948 passed, 29 skipped, 1 xfailed** (2026-11-22)
- Live API smoke test: 16/16 checks (2026-11-22)
- Offline verifier parity: Python / JS / standalone / Studio all passing
- Conformance self-test vectors: 24/24 (see [CONFORMANCE.md](CONFORMANCE.md))

## Shipped capabilities

- **Trusted verification (offline):** the runtime verifier resolves signers against a
  signed trusted-key registry (public keys only) with a signed revocation list;
  unregistered or revoked keys fail at a named step. Without a registry configured,
  the verifier reports `simulated` mode instead of silently claiming a real check.
- **Qualified timestamp option:** RFC 3161 verification via `openssl ts`, with
  trusted-CA chain mode when a bundle is supplied and an explicitly labelled
  structural mode otherwise. Optional; never required for receipt validity.
- **Per-hop delegation signatures:** multi-hop chains are verified cryptographically
  where `delegator_public_key` is present; older hops are reported as `legacy skip`,
  never as verified.
- **Production KMS gate:** the `ENV` key provider is refused when `ENV=production`
  unless explicitly approved.
- **DQI v2 (advisory only):** `vap-dqi-2.0` agentic-web profile with content-aware,
  anti-gaming checks, and `dqi.v2` clinical profile in PersonaVault. VAP DQI is
  frozen into the signed receipt at signing time; v1 snapshots remain verifiable.
  No integrity check, approval, or enforcement path reads a DQI score.
- **Signed offline verification bundle:** a third party can verify a receipt with
  no account, network or platform trust. All verifier surfaces share one engine,
  and differential tests assert identical verdicts. See [OFFLINE_BUNDLE.md](OFFLINE_BUNDLE.md).
- **Conformance self-tests:** offline vector runner for ARAS v1.0 scoring, A2A gateway
  lifecycle and MCP JSON-RPC 2.0. See [CONFORMANCE.md](CONFORMANCE.md). These are
  self-tests, **not** external certification.
- **DID boundary:** only local `did:mesh` / `did:human` methods resolve; `did:key` and
  `did:web` fail closed. See [DID_INTEROP.md](DID_INTEROP.md). Receipt verification
  uses embedded public keys, not a DID resolver.
- **Dashboard Merkle batches:** build a batch from leaf hashes and inspect inclusion
  proofs; local anchor is always written, chain broadcast is a separate explicit action.
- Advisory outcome linkage and counterfactual replay-to-calibration reports
  (never alter signed receipts or approvals).
- Fail-closed Guardian enforcement with canonical VAP receipts (code emits
  receipt `version = "3.0"`). ML-DSA-87 hybrid signing is org-controlled via
  the `/organization` preference; the receipt proof carries `pq_signature`
  and `pq_signer_public_key` when enabled, and `signature_algorithm` becomes
  `Ed25519+ML-DSA-87`. Enabling without liboqs/keys is rejected. See
  [PQ_EVIDENCE.md](PQ_EVIDENCE.md) for a real dual-signed receipt and its
  offline verification result.
- Anchoring has two layers: a real local/offline Merkle-root anchor produced for
  every receipt, and an optional public/private chain broadcast via web3. Multi-hash batches let one anchor cover many receipts with per-leaf inclusion proofs. A
  Polygon Amoy testnet transaction and block were confirmed 2026-11-21 — see
  [ANCHORING_EVIDENCE.md](ANCHORING_EVIDENCE.md). Development defaults to
  `BLOCKCHAIN_MODE=SIMULATED` (offline anchor only, no chain spend); real chain
  mode is enabled by configuration and fails closed on chain errors. Blockchain
  is optional for specific use cases, not required for every high-risk sector.
- Persisted, org-scoped agent-to-agent messaging and a local A2A v1.0 gateway
  (Agent Card, JSON-RPC, SSE streaming, push notifications, task lifecycle).
- Trust lifecycle and reputation protocol with versioned formulas, signed
  transition receipts, and a dispute mechanism.
- Signed substrate events, cross-reference service and absence/BYPASS audit.
- Real policy what-if replay over historical receipts; real HITL decision
  queue with signed approve/deny receipts.
- Persisted commerce: negotiations, escrow, wallet transfers and settlement
  gated by signed intent and real on-chain proof verification.
- Operational dashboard: Today action queue, Deals lifecycle, Governance
  control room, Agent lifecycle, and VAP search/verify/export.

## Forward-looking only (not present as shipped or Roadmap)

- SCITT transparency service — discussion document only.
- External A2A conformance/certification (local self-test vectors exist; see above).
- External ARAS validation against a book of real claims (covariates are extractable,
  loss correlation is an open empirical question).
- Any defined interoperation or shared trust fabric between VeriLinkOS and PersonaVault.
- VAP **v3.5** is a target specification; the reference implementation emits
  receipts with `version = "3.0"`. The hybrid ML-DSA-87 signature profile is
  already implemented in that v3.0 receipt format (org-controlled via
  `/organization`), and the offline verifier checks both signatures. SCITT
  alignment remains discussion-only.
- Rego/OPA in-browser policy playground; privacy and sovereignty proposals.

## Note on numbers in older documents

Whitepapers and blog posts that cite "197+ tests", "913 tests" or "935 tests"
were accurate when written; the current VeriLinkOS baseline is **948 passing
tests** as of 2026-11-22. PersonaVault has its own separate suite (897 passing)
and is not included in this number.
