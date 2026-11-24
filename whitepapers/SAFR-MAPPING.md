# SAFR ↔ VeriLinkOS: an honest control-by-control mapping

**Version:** 2.0 — 24 November 2026
**Basis:** SAFR v1.1 (9 October 2026; first published 3 July 2026)
**Supersedes:** version 1.0 (September 2026), which mapped SAFR v1.0 to an aspirational VAP v3.5 profile and overstated shipped capabilities.
**Status:** reference mapping. Not regulatory guidance, not certification, not endorsement.

---

## 0. Executive summary

SAFR and VeriLinkOS were designed against the same problem. SAFR states:

> "What is needed is a governance layer that operates at the point of action. For each agentic system and action type, institutions need to establish what the system is authorised to do, how proposed actions are assessed at runtime before execution, and what records are retained to support review, accountability, and remediation when outcomes diverge from intent."

VeriLinkOS addresses the same gap from the evidence side. The layers are complementary:

- **SAFR** defines an institution-internal runtime governance architecture: agent identity, a controls repository, a disposition engine and an append-only audit log.
- **VeriLinkOS** supplies portable, independently verifiable evidence about the relationship between pre-execution authorisation and the operation actually executed.

The claim being tested is therefore narrow: not "we also have runtime governance" — SAFR and several commercial vendors have that — but **portable, independently verifiable authorisation-to-execution binding**. The core binding is implemented: a signed witness is written into a generated receipt's evidence and verified offline. Sections 9–10 state what is shipped and what remains.

**At a glance:** strong alignment on the problem and most runtime controls; ahead on portable verification and execution-time binding; remaining work is a durable replicated audit log and external conformance evidence.

## 1. Scope, status and how to read this document

**SAFR** (*Safeguards for Agentic Finance at Runtime*) is an industry reference approach published under MAS's BuildFin.ai initiative. The paper states that it "does not constitute regulatory guidance or supervisory expectations", is provided "as-is", and is "not a managed service". It invites practitioners to contribute pilot findings and identify gaps.

**VeriLinkOS** is built for **sovereign deployment**: the operator runs it inside its own infrastructure, under its own keys and data-governance controls. That is a deliberate design choice for regulated and high-liability environments, not a missing capability. The public-verification interfaces are implemented and operator-configurable; the project does not run a hosted service on anyone's behalf.

This document compares SAFR v1.1 with the VeriLinkOS authorisation-to-execution binding contract (VAP is the Verifiable Action Protocol; ARAS is an advisory risk-assessment standard; loop governance is a recent addition to the runtime). The contract itself is not reproduced here. Status labels are used deliberately:

- **SHIPPED** — present in the codebase and exercised by tests;
- **CONFIGURED BUT OFF** — implemented, disabled by default in development configuration;
- **SOVEREIGN-DEPLOYABLE** — production-ready inside the operator's own boundary;
- **ONLINE-READY** — interfaces and configuration exist so an operator can expose them publicly; the project hosts no service;
- **SPECIFIED** — contract defined and being implemented;
- **IN PROGRESS** — implementation underway at the time of writing;
- **GAP** — not covered by the current contract; being addressed where security-relevant.

No statement here should be read as a claim of MAS/FCA/IMDA endorsement, adoption, attribution, compliance or certification. SAFR v1.1 contains no reference to VeriLinkOS.

## 2. Runtime components

| SAFR component | VeriLinkOS reality | Status |
|---|---|---|
| **Agent Identity** — bind action to registered agent, verify before other evaluation | `did:mesh` / `did:human`, Ed25519 keys, agent signup, scoped tool tokens; `/.well-known/did.json`, `/.well-known/jwks.json` | **SHIPPED** |
| **Controls Repository** — configurable rulebook, machine-readable mandates, validity period, principal authority | policy engine, capability grants with spend limits and expiry, HITL, signed discovery manifests | **SHIPPED** (partial: no explicit delegation-depth semantics) |
| **Disposition Engine** — deterministic evaluation of proposed actions | Guardian `_evaluate_policy` (pure evaluation); binding decisions deny/escalate/allow/observe | **SHIPPED** |
| **Audit Log** — tamper-evident, append-only decision record | signed receipts with canonical leaf hashes; opt-in hash-chained JSONL replica (ZT-06) | **SHIPPED** (durability is opt-in and deployment-local) |
| **Native integration** — instrument the agent | SDK adapters, Guardian API | **SHIPPED** |
| **Gateway integration** — intercept outbound calls | MCP `tools/call` dispatch, governance proxy | **SHIPPED** |

## 3. Governance Envelope ↔ ActionIntent

| SAFR requirement | VeriLinkOS | Status |
|---|---|---|
| Action: type, scope, parameters | `operation`, `tool_name`, `target`, `callable_id`, `arguments_hash` | **SPECIFIED** |
| Action trace: tools used, data retrieved, checks performed, evidence relied on | `action_trace_hash` bound into intent and proof | **SHIPPED** |
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
| Observe — execute with structured observation | first-class `observe` disposition on coalesced actions | **SHIPPED** |
| Escalation volume, turnaround with default-block, reviewer authority | signed escalation fields and reviewer-authority requirement in the proof | **SHIPPED** |

## 6. Audit record and integrity controls

| SAFR requirement | VeriLinkOS | Status |
|---|---|---|
| Tamper-evident, append-only record of every governance decision | signed receipts; hash-chained replica | **SHIPPED** (replica opt-in) |
| Entry captures envelope, mandate, outcome, rules, basis, elapsed time | binding block captures intent/proof hashes, policy version, loop disposition, callable, argument hash, timestamps, outcome digest | **SPECIFIED** |
| Independent of any party's post-hoc characterisation | offline verifier checks `evidence.execution_binding` in the generated receipt | **SHIPPED** |
| Hashing, signatures, append-only storage, timestamping, key management | Ed25519 + optional ML-DSA-87, signer registry; `timestamp` check is `skip` in the SDK verifier | **PARTIAL** |

## 7. Capabilities that were understated in version 1.0

Version 1.0 understated three capabilities. Code inspection confirms the following corrections; each is phrased as a precise claim rather than a broad one.

### 7.1 Blockchain anchoring is real, and disabled in development

`verilink/blockchain.py` implements `BLOCKCHAIN_MODE` with modes `SIMULATED`, `DISABLED`, `POLYGON_AMOY`, `POLYGON_MAINNET`, `PRIVATE_LEDGER`, an RPC URL, chain ID, anchor contract and KMS-backed anchor key, and `anchor_receipt(merkle_root, action_type)`. `verilink/services/onchain_proof.py` queries the chain for a transaction receipt and verifies the expected transfer, failing closed when the mode is `SIMULATED` or `DISABLED` or no RPC is configured. Current `.env` sets `BLOCKCHAIN_MODE=SIMULATED`; the local Merkle anchor remains active. **Precise claim:** real chain anchoring is implemented and deployment-configured; no production broadcast is claimed for the current development configuration.

### 7.2 Sovereign deployment by design, with online-ready interfaces

VeriLinkOS is production-ready for deployment inside the operator's own boundary. Key custody, data retention and public exposure remain decisions of the operator — an intentional property for regulated environments and a direct fit for data-sovereignty requirements.

`/.well-known/jwks.json` (root alias and trust-passport route), `/.well-known/did.json`, signed trust-anchor endpoints and a configurable `VERILINK_SITE_URL` are implemented. An operator can expose these endpoints publicly so counterparties can verify artifacts without contacting a VeriLinkOS-operated service. There is no `verify.verilinkos.com` endpoint in this codebase and the project hosts no verification service. **Precise claim:** the deployment is sovereign by design and online-ready when the operator chooses; this is a deployment topology, not a product limitation.

### 7.3 Annex G forensic packages are real, and scoped

`POST /v2/assets/{asset_id}/evidence/{incident_id}` generates an evidence package via `AssetProtectionService.generate_evidence_package`, including asset state, endorsements, receipts and verification logs; `POST /v2/assets/evidence/verify` verifies a third-party package. The SDK exposes `generate_forensic_package`. **Precise claim:** Annex G packages are generated for asset/incident forensics — not converted automatically from every VAP receipt, not a statutory form, and not court-certified.

## 8. Singapore legal position — precise claim

Cryptographic receipts are valuable under Singapore law and **can constitute electronic evidence in court**. The project's precise formulation is:

> **VeriLinkOS produces court-admissible electronic records in principle — records with the integrity, signature and timestamp properties that engage the statutory presumptions for electronic evidence. Whether a court admits a specific receipt, and what weight it gives it, is for that court.**

Primary sources:

- **Evidence Act 1893 s 116A(1):** unless evidence sufficient to raise doubt is adduced, where a device or process is one that ordinarily produces or accurately communicates an electronic record, the court presumes that on the occasion in question the device or process produced or accurately communicated it.
- **Evidence Act s 64 Explanation 3:** an electronic copy accurately reflecting the document is primary evidence.
- **Electronic Transactions Act 2010 s 19(1):** in proceedings involving a *secure electronic record*, it is presumed, unless contrary evidence is adduced, that it has not been altered since the point in time to which its secure status relates. "Secure" is a statutory condition, not merely "has a hash".
- **[2025] SGCA 21 (GIL v PP):** s 116A(1) presumes accurate communication by the producing device or process — **not** the truth of the record's contents. Admitted evidence may receive little, no or critical weight depending on the whole record. Machine-generated analysis still requires proof of reliability.

We do **not** claim: guaranteed admission, a judicial ruling on our receipts, a statutory form, certification, insurance coverage, or that cryptographic integrity proves an external event occurred.

## 9. Gaps identified in version 2.0 — now closed

The earlier version of this mapping listed seven gaps. The security-relevant and table-stakes ones have since been closed:

1. Signed action trace — now bound into the intent and proof.
2. First-class `Observe` disposition — now emitted for coalesced actions.
3. Escalation volume, turnaround, default-block and reviewer authority — now signed fields in the authorisation proof.
4. Authoritative registry selection — now a signed field checked at execution.
5. Mandatory signed timestamp — the authorisation timestamp is signed and re-verified at execution.

Two remain by design: evidence-quality thresholds stay advisory (ARAS is not an approval gate), and a replicated audit log is an operator deployment decision rather than a platform default. The protocol freeze applies only to speculative expansion, not to identified security gaps.

## 10. Where the specification goes beyond SAFR

All of the following are defined by the specification and now implemented in the core binding path:

1. Portable independent verification by a receiving firm without the operator's infrastructure.
2. Mandatory executor-side re-binding to the actual callable and arguments.
3. Explicit authorisation invalidation with concrete failure semantics.
4. Durable execution state machine with `UNKNOWN` and mandatory reconciliation.
5. Versioned canonicalisation with domain separation and rejection of unknown profiles.
6. Verifier semantics: `pass`/`fail`/`skip`, skip never pass, absent distinct from invalid.

## 11. Verdict

- **Alongside** SAFR on the problem and most runtime controls.
- **Ahead** on portable independent verification, execution-time binding, crash recovery and verifier semantics — implemented in the core binding path.
- **Remaining by design** on evidence-quality thresholds (advisory) and replicated audit durability (operator decision).
- **Not first.** SAFR is a named, published, industry-developed reference architecture in the target jurisdiction.

The highest-value contribution now is not another mapping document. It is the VAP conformance family (canonicalisation vectors, signature cases, delegation, tampering, binding, replay, trust state, evidence semantics) and the proof-bundle fixtures, offered to the BuildFin.ai working group and the FCA Agentic Policy Sprint as practical evidence.

## 12. Claims this document does not make

MAS, FCA or IMDA endorsement or adoption; VeriLinkOS compliance with SAFR, the EU AI Act, IMDA MGF or FCA expectations; certification; guaranteed admissibility; that the binding implementation is complete; that external-world effects or decision correctness are proved.

## 13. References

1. MAS, [*Safeguards for Agentic Finance at Runtime*](https://www.mas.gov.sg/publications/monographs-or-information-paper/2026/safeguards-for-agentic-finance-at-runtime), v1.1, 9 Oct 2026.
2. IMDA, [*Model AI Governance Framework for Agentic AI*](https://www.imda.gov.sg/about-imda/emerging-technologies-and-research/artificial-intelligence), v1.5, May/June 2026.
3. FCA, [*Our approach to Agentic AI*](https://www.fca.org.uk/firms/innovation/ai-approach/agentic-ai), 24 Nov 2026.
4. Singapore [Evidence Act 1893](https://sso.agc.gov.sg/Act/EA1893), s 116A and s 64 Explanation 3.
5. Singapore [Electronic Transactions Act 2010](https://sso.agc.gov.sg/Act/ETA2010), s 19.
6. [*GIL v Public Prosecutor* [2025] SGCA 21](https://www.elitigation.sg/gd/s/2025_SGCA_21).
7. VeriLinkOS authorisation-to-execution binding and proof-bundle specifications (reference implementation; not publicly linked).
