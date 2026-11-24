# SAFR ↔ VeriLinkOS: an honest control-by-control mapping

**Version:** 2.0 — 24 November 2026
**Basis:** SAFR v1.1 (9 October 2026; first published 3 July 2026)
**Supersedes:** version 1.0 (September 2026), which mapped SAFR v1.0 to an aspirational VAP v3.5 profile and overstated shipped capabilities.
**Status:** reference mapping. Not regulatory guidance, not certification, not endorsement.

---

## 0. Scope and honesty

**SAFR** (*Safeguards for Agentic Finance at Runtime*) is an industry reference approach published under MAS's BuildFin.ai initiative. The paper states that it "does not constitute regulatory guidance or supervisory expectations", is provided "as-is", and is "not a managed service". It invites practitioners to contribute pilot findings and identify gaps.

**VeriLinkOS** is a locally deployable platform. It does not operate a hosted verification service. Its binding specification ([`PROOF_CARRYING_EXECUTION.md`](https://github.com/rajinderjhol/VeriLinkOS)) is specified, **not yet implemented**.

The earlier mapping described capabilities as shipped when they were partly or wholly aspirational. This version distinguishes:

- **SHIPPED** — present in the codebase and exercised by tests;
- **CONFIGURED BUT OFF** — implemented, disabled by default in development configuration;
- **ONLINE-READY** — endpoints and configuration exist, but no service is hosted by us;
- **SPECIFIED** — designed but not implemented;
- **GAP** — not covered by the current specification.

No statement here should be read as a claim of MAS/FCA/IMDA endorsement, adoption, attribution, compliance or certification. SAFR v1.1 contains no reference to VeriLinkOS.

## 1. Executive summary

SAFR and VeriLinkOS were designed against the same problem. SAFR states:

> "What is needed is a governance layer that operates at the point of action. For each agentic system and action type, institutions need to establish what the system is authorised to do, how proposed actions are assessed at runtime before execution, and what records are retained to support review, accountability, and remediation when outcomes diverge from intent."

That is the same gap our binding specification addresses. The layers are complementary:

- **SAFR** defines an institution-internal runtime governance architecture: identity, controls, disposition, audit log.
- **VeriLinkOS** supplies portable, independently verifiable evidence about the relationship between pre-execution authorisation and the operation actually executed.

The differentiated claim is **not** "we also have runtime governance". SAFR has that, and so do several commercial vendors. The differentiated claim is the **portable, independently verifiable authorisation-to-execution binding** — once implemented and demonstrated.

## 2. Runtime components

| SAFR component | VeriLinkOS reality | Status |
|---|---|---|
| **Agent Identity** — bind action to registered agent, verify before other evaluation | `did:mesh` / `did:human`, Ed25519 keys, agent signup, scoped tool tokens; `/.well-known/did.json`, `/.well-known/jwks.json` | **SHIPPED** |
| **Controls Repository** — configurable rulebook, machine-readable mandates, validity period, principal authority | policy engine, capability grants with spend limits and expiry, HITL, signed discovery manifests | **SHIPPED** (partial: no explicit delegation-depth semantics) |
| **Disposition Engine** — deterministic evaluation of proposed actions | Guardian `_evaluate_policy` (pure evaluation), allow/deny/escalate | **SHIPPED** (no distinct `Observe` outcome) |
| **Audit Log** — tamper-evident, append-only decision record | signed receipts with canonical leaf hashes; opt-in hash-chained JSONL replica (ZT-06) | **SHIPPED** (durability is opt-in and deployment-local) |
| **Native integration** — instrument the agent | SDK adapters, Guardian API | **SHIPPED** |
| **Gateway integration** — intercept outbound calls | MCP `tools/call` dispatch, governance proxy | **SHIPPED** |

## 3. Governance Envelope ↔ ActionIntent

| SAFR requirement | VeriLinkOS | Status |
|---|---|---|
| Action: type, scope, parameters | `operation`, `tool_name`, `target`, `callable_id`, `arguments_hash` | **SPECIFIED** |
| Action trace: tools used, data retrieved, checks performed, evidence relied on | Not in the binding contract | **GAP** |
| Context metadata: identity, mandate, system state, policy constraints | `agent_did`, `signing_key_id`, `canonical_profile`, grant scope | **SPECIFIED** |
| "Envelope should not be treated as self-verifying" | Trust-boundary section: signatures establish consistency, not truth; skip never equals pass | **SPECIFIED** |
| Bound to verified agent/session/mandate, protected from alteration | `intent_hash`, domain-separated canonicalisation, agent signature | **SPECIFIED** |
| Check envelope fields against independently captured records | Executor re-hashes actual invocation arguments and resolves the actual callable immediately before execution | **SPECIFIED** (stronger than SAFR at the execution moment) |

## 4. Mandates and authority

| SAFR requirement | VeriLinkOS | Status |
|---|---|---|
| Machine-readable delegated authority with limits and conditions | capability grants, `AuthorizationProof` | **SPECIFIED** |
| "An agent cannot extend the scope of a mandate through its own reasoning" | One proof per `intent_hash`, no fallback to prior authorisation | **SPECIFIED** |
| "Prior authorisation should not carry forward" | Explicit policy/key/registration invalidation with deny semantics | **SPECIFIED** |
| Validity period, principal authority hierarchy | `expiry`, `DecisionAuthorization.authorizer_did`; no delegation-depth field | **PARTIAL** |
| Exposure limits, rate limits | spend limits, reputation-based throttling, loop guard | **SHIPPED** |
| Evidence-quality threshold routes to human review | ARAS covariates are deliberately advisory | **DELIBERATE DIFFERENCE** |

## 5. Disposition outcomes

SAFR defines four outcomes. VeriLinkOS currently has three.

| SAFR | VeriLinkOS | Status |
|---|---|---|
| Deny | fail closed, denial receipt | **SHIPPED** |
| Escalate | HITL pending receipt, `requires_hitl` | **SHIPPED** |
| Auto-Execute | execute under single-use consumption | **SPECIFIED** |
| Observe — execute with structured observation | advisory `loop_rate` / ARAS only | **GAP** |
| Escalation volume, turnaround with default-block, reviewer authority | HITL approvals recorded; no operational contract | **GAP** |

## 6. Audit record and integrity controls

| SAFR requirement | VeriLinkOS | Status |
|---|---|---|
| Tamper-evident, append-only record of every governance decision | signed receipts; hash-chained replica | **SHIPPED** (replica opt-in) |
| Entry captures envelope, mandate, outcome, rules, basis, elapsed time | binding block captures intent/proof hashes, policy version, loop disposition, callable, argument hash, timestamps, outcome digest | **SPECIFIED** |
| Independent of any party's post-hoc characterisation | independent offline verifier checks the bundle without the producer | **SHIPPED for receipts; not yet for binding** |
| Hashing, signatures, append-only storage, timestamping, key management | Ed25519 + optional ML-DSA-87, signer registry; `timestamp` check is `skip` in the SDK verifier | **PARTIAL** |

## 7. Capabilities that were understated in version 1.0

The user corrected three of the earlier document's over-cautious removals. Code inspection confirms the corrections.

### 7.1 Blockchain anchoring is real, and disabled in development

`verilink/blockchain.py` implements `BLOCKCHAIN_MODE` with modes `SIMULATED`, `DISABLED`, `POLYGON_AMOY`, `POLYGON_MAINNET`, `PRIVATE_LEDGER`, an RPC URL, chain ID, anchor contract and KMS-backed anchor key, and `anchor_receipt(merkle_root, action_type)`. `verilink/services/onchain_proof.py` queries the chain for a transaction receipt and verifies the expected transfer, failing closed when the mode is `SIMULATED` or `DISABLED` or no RPC is configured. Current `.env` sets `BLOCKCHAIN_MODE=SIMULATED`; the local Merkle anchor remains active. **Precise claim:** real chain anchoring is implemented and deployment-configured; no production broadcast is claimed for the current development configuration.

### 7.2 Online-ready, not hosted

`/.well-known/jwks.json` (root alias and trust-passport route), `/.well-known/did.json`, signed trust-anchor endpoints and a configurable `VERILINK_SITE_URL` are implemented. A deployment operator can expose them. There is no `verify.verilinkos.com` endpoint in this codebase and no hosted verification service operated by us. **Precise claim:** online-ready endpoints exist; no hosted service is claimed.

### 7.3 Annex G forensic packages are real, and scoped

`POST /v2/assets/{asset_id}/evidence/{incident_id}` generates an evidence package via `AssetProtectionService.generate_evidence_package`, including asset state, endorsements, receipts and verification logs; `POST /v2/assets/evidence/verify` verifies a third-party package. The SDK exposes `generate_forensic_package`. **Precise claim:** Annex G packages are generated for asset/incident forensics — not converted automatically from every VAP receipt, not a statutory form, and not court-certified.

## 8. Singapore legal position — precise claim

Cryptographic receipts are valuable under Singapore law and **can constitute electronic evidence in court**. The precise formulation, already maintained in [`EVIDENCE_CAPABILITY_AND_LEGAL_FOUNDATION.md`](https://github.com/rajinderjhol/VeriLinkOS), is:

> **VeriLinkOS produces court-admissible electronic records in principle — records with the integrity, signature and timestamp properties that engage the statutory presumptions for electronic evidence. Whether a court admits a specific receipt, and what weight it gives it, is for that court.**

Primary sources:

- **Evidence Act 1893 s 116A(1):** unless evidence sufficient to raise doubt is adduced, where a device or process is one that ordinarily produces or accurately communicates an electronic record, the court presumes that on the occasion in question the device or process produced or accurately communicated it.
- **Evidence Act s 64 Explanation 3:** an electronic copy accurately reflecting the document is primary evidence.
- **Electronic Transactions Act 2010 s 19(1):** in proceedings involving a *secure electronic record*, it is presumed, unless contrary evidence is adduced, that it has not been altered since the point in time to which its secure status relates. "Secure" is a statutory condition, not merely "has a hash".
- **[2025] SGCA 21 (GIL v PP):** s 116A(1) presumes accurate communication by the producing device or process — **not** the truth of the record's contents. Admitted evidence may receive little, no or critical weight depending on the whole record. Machine-generated analysis still requires proof of reliability.

We do **not** claim: guaranteed admission, a judicial ruling on our receipts, a statutory form, certification, insurance coverage, or that cryptographic integrity proves an external event occurred.

## 9. Genuine gaps in the current specification

Recorded honestly, not silently absorbed into "aligned":

1. Signed action trace behind a proposal (SAFR requires it; our `ActionIntent` does not carry it).
2. A first-class `Observe` disposition with a structured observation record.
3. Escalation volume, turnaround timeout with default-block, and reviewer-authority contract.
4. Authoritative registry selection when an agent is registered in several registries.
5. Evidence-quality as a control input (we keep ARAS advisory by design).
6. Durable replicated audit log (our replica is opt-in and deployment-local).
7. Mandatory signed timestamp (SDK verifier reports `timestamp` as `skip`).

None of these are in the current implementation milestone. Do not expand protocol scope to absorb them without a pilot-driven reason.

## 10. Where the specification goes beyond SAFR

All of the following are **specified, not shipped**:

1. Portable independent verification by a receiving firm without the operator's infrastructure.
2. Mandatory executor-side re-binding to the actual callable and arguments.
3. Explicit authorisation invalidation with concrete failure semantics.
4. Durable execution state machine with `UNKNOWN` and mandatory reconciliation.
5. Versioned canonicalisation with domain separation and rejection of unknown profiles.
6. Verifier semantics: `pass`/`fail`/`skip`, skip never pass, absent distinct from invalid.

## 11. Verdict

- **Alongside** SAFR on the problem and most runtime controls.
- **Ahead** on portable independent verification, execution-time binding, crash recovery and verifier semantics — as a specification, not a shipped product.
- **Behind** on action trace, `Observe`, escalation operations, registry authority selection, audit durability and signed timestamps.
- **Not first.** SAFR is a named, published, industry-developed reference architecture in the target jurisdiction.

The highest-value contribution now is not another mapping document. It is the VAP conformance family (canonicalisation vectors, signature cases, delegation, tampering, binding, replay, trust state, evidence semantics) and the proof-bundle fixtures, offered to the BuildFin.ai working group and the FCA Agentic Policy Sprint as practical evidence.

## 12. Claims this document does not make

MAS, FCA or IMDA endorsement or adoption; VeriLinkOS compliance with SAFR, the EU AI Act, IMDA MGF or FCA expectations; certification; guaranteed admissibility; that the binding is implemented; that external-world effects or decision correctness are proved.

## 13. References

1. MAS, *Safeguards for Agentic Finance at Runtime*, v1.1, 9 Oct 2026.
2. IMDA, *Model AI Governance Framework for Agentic AI*, v1.5, May/June 2026.
3. FCA, *Our approach to Agentic AI*, 24 Nov 2026.
4. Singapore Evidence Act 1893, s 116A and s 64 Explanation 3.
5. Singapore Electronic Transactions Act 2010, s 19.
6. *GIL v Public Prosecutor* [2025] SGCA 21.
7. VeriLinkOS, *Proof-Carrying Execution* and *Proof Bundle v0* specifications.
