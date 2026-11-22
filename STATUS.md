# Implementation Status Addendum — 2026-11-22

> This page records what the companion VeriLinkOS code repository actually
> implements today. Where this page conflicts with older whitepapers or blog
> posts, **this page wins**. All claims here are about local implementation,
> not external certification. PersonaVault is a separate repository with its
> own status; no interoperation between the two systems is defined or claimed.

## Verification baseline

- Reference implementation: `github.com/rajinderjhol/VeriLinkOS`
- Full test suite: **1016 passed, 29 skipped, 1 xfailed** (2026-11-22)
- Live API smoke test: 16/16 checks (2026-11-22)
- Offline verifier parity: Python / JS / standalone / Studio all passing
- Conformance self-test vectors: 37/37 (see [CONFORMANCE.md](CONFORMANCE.md))

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
- **ARAS v1.0 covariate reporting:** `POST /v1/analytics/aras/report` extracts
  covariates from supplied receipts and explicit deployment facts, scores them
  per the published methodology, and reports the tier. Covariates that cannot
  be sourced stay `null` and are scored worst-case — no invented values. The
  endpoint is advisory and never affects an integrity check or approval.
- **Risk exposure and loss model:** there is no validated loss model, so the
  Guardian card shows `—`. The quote endpoint now returns an explicitly
  illustrative, non-binding indication; no carrier, discount, coverage limit or
  VAP-proof claim is made. See
  [RISK_EXPOSURE_AND_LOSS_MODEL.md](RISK_EXPOSURE_AND_LOSS_MODEL.md) for the
  formulas that do exist and the gaps to production readiness.
- **Dashboard value audit:** an honest classification of all 43 tabs into closed
  loops, conditional surfaces and dead ends, with an activation plan. Empty
  collections now explain why they are empty and offer the action that fills
  them across the top 10 tabs. The four Consoles workspaces (Guardian, Trust
  Center, Insurance, Commerce) no longer fabricate data on failure. See
  [DASHBOARD_VALUE_AUDIT.md](DASHBOARD_VALUE_AUDIT.md).
- **Link Safety decisions:** a URL check produces a signed advisory `LNK-*`
  decision with attributable intel signals (source, confidence, timestamp),
  SSRF-safe redirect analysis and a signed bundle export. The dashboard tab is
  operational: allow/block watchlist CRUD (soft-delete), decision detail,
  append-only analyst reviews and bundle download. Signed decisions are
  immutable and cannot be deleted. These are evidence, not an execution gate.
  See [LINK_SAFETY_EVIDENCE.md](LINK_SAFETY_EVIDENCE.md).
- **Trustable links and QR:** signed link declarations with a published well-known
  key, client-side WebCrypto verification, QR encoding and revocation. A dedicated
  tab issues, verifies and manages declarations. See [TRUSTABLE_LINKS_AND_QR.md](TRUSTABLE_LINKS_AND_QR.md).
- **Single deployment URL config:** `VERILINK_BASE_URL` / `VERILINK_PUBLIC_URL` /
  `VERILINK_ISSUER_URL` drive deployment URLs; `VERILINK_SITE_URL` /
  `VERILINK_DOCS_URL` drive corporate identity and documentation links.
- **Multi-tenant trust:** per-tenant derived signing keys, tenant-scoped
  well-known discovery, verified-domain binding for link declarations and
  federation peer anchors. See [DEPLOYMENT_CONFIG.md](DEPLOYMENT_CONFIG.md).
- **Guardian console:** the workspace now reports the Guardian kernel's own
  status, HITL quality, constitutional metrics/rules and circuit-breaker state,
  and exposes enforce/arbitrate actions — each from its owning backend service.
  The Kernel section is navigable via a section entry and four sub-tabs (status,
  HITL, constitutional, tools). Constitutional pass-rate and average-time are
  not instrumented and are displayed as unavailable rather than fabricated;
  the status latency is labelled as a fixed target, not a measurement.
- **Dual-Use section:** a navigable Guardian section with Posture, Policy and
  Evidence sub-tabs. Posture records the original Neutral Data Embassy design
  intent and possible extension to submarine cables and space-based data
  centres as forward-looking context only — no cable, satellite or orbital
  telemetry monitoring is claimed, and the system does not determine legal
  status under humanitarian law. Policy and Evidence bind to real `/v1/policies`
  and signed `/v1/vap/receipts` data; the fabricated detection scores and alerts
  were removed. The Audit Trail now reads real `/v1/audit/logs`. Control-layer
  KPIs derive from live data with `—` where the source is absent; the status badge
  reads `/v1/guardian/status`; emergency override calls the real kill-switch
  endpoint; the autonomy selector is labelled advisory only; and the regulatory
  card states design intent rather than compliance.
- **ADEM primitives (feature-flagged, default off):** signed JWS/CWT emblems for
  the IETF DIEM workstream (`draft-linker-diem-adem-core-00`,
  `draft-ietf-diem-requirements-03`) with a `/.well-known/adem/{asset_id}`
  discovery endpoint and claim validators. Enabled by `ADEM_ENABLED=true`. This
  is an implementation of an IETF draft — not certification or endorsement by
  the ICRC, and no protection status is conferred by an emblem.
- **Browser authentication:** httpOnly session cookie only — no credentials in
  localStorage; login no longer exposes the deployment API key; per-org keys
  are created once and managed in Profile settings. See
  [DASHBOARD_VALUE_AUDIT.md](DASHBOARD_VALUE_AUDIT.md).
- **Conformance self-tests:** offline vector runner for ARAS v1.0 scoring, A2A gateway
  lifecycle, MCP JSON-RPC 2.0 and a labelled Link Safety corpus. See
  [CONFORMANCE.md](CONFORMANCE.md). These are self-tests, **not** external certification.
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
- Dual-use applicability to Neutral Data Embassies, submarine cables and
  space-based infrastructure beyond the policy/evidence substrate described
  above — design context only, not implemented monitoring or certification.
- Automated domain-verification challenges and external KMS backing for tenant keys.
- VAP **v3.5** is a target specification; the reference implementation emits
  receipts with `version = "3.0"`. The hybrid ML-DSA-87 signature profile is
  already implemented in that v3.0 receipt format (org-controlled via
  `/organization`), and the offline verifier checks both signatures. SCITT
  alignment remains discussion-only.
- Rego/OPA in-browser policy playground; privacy and sovereignty proposals.

## Note on numbers in older documents

Whitepapers and blog posts that cite "197+ tests", "913 tests", "935 tests",
"948 tests", "976 tests", "982 tests" or "986 tests" were accurate when written;
the current VeriLinkOS baseline is **1016 passing tests** as of 2026-11-22. PersonaVault has
its own separate suite (897 passing) and is not included in this number.
