# Verifiable Action Protocol (VAP) v3.5 Specification
**Open Specification for Cryptographic AI Governance & Action Provenance**

---

## 1. Abstract
The Verifiable Action Protocol (VAP) v3.5 is an open, vendor-neutral specification for generating, signing, batching, and verifying cryptographic proof of AI decisions, tool executions, quality evaluations, token costs, and compliance filings. VAP creates tamper-evident receipts that bind model identity, input context, policy enforcement, quality scores, token costs, and Merkle tree roots into Ed25519-signed digital evidence packages. VAP v3.5 introduces native support for hybrid post-quantum signatures (ML-DSA-87) to ensure long-term integrity and is currently being drafted as an IETF Internet-Draft in alignment with IETF SCITT (Supply Chain Integrity, Transparency, and Trust) standards.

---

## 2. Receipt Data Model

A canonical VAP Receipt comprises seven core blocks:

```json
{
  "receipt_id": "VAP-20260807-AID8X9-4F8A",
  "action": {
    "type": "ai_decision",
    "target": "financial_agent",
    "parameters": { "prompt": "Execute transfer", "amount": 50000 },
    "metadata": { "model_name": "gpt-4o", "session_id": "sess-9a8b7c" }
  },
  "decision": {
    "verdict": "allow",
    "confidence": 98.5
  },
  "context": {
    "policy_id": "pol_financial_transfers_v2",
    "timestamp": "2026-08-07T13:00:00Z"
  },
  "quality_scores": {
    "hallucination_score": 0.02,
    "toxicity_score": 0.0,
    "factual_consistency": 0.98,
    "bias_score": 0.01,
    "quality_gate_passed": true
  },
  "cost": {
    "model_name": "gpt-4o",
    "prompt_tokens": 1500,
    "completion_tokens": 500,
    "total_tokens": 2000,
    "estimated_cost_usd": 0.00875,
    "currency": "USD",
    "cost_center": "finance"
  },
  "proof": {
    "scheme": "ed25519_mldsa87_hybrid",
    "signature": "3a4b5c...6d7e8f",
    "pq_signature": "f9e8d7...c6b5a4",
    "signer_public_key": "a1b2c3...d4e5f6",
    "pq_public_key": "c6b5a4...f9e8d7",
    "merkle_root": "8f7e6d...5c4b3a",
    "merkle_path": ["e1f2..."],
    "anchors": [
      {
        "type": "public_evm",
        "chain": "Polygon Mainnet",
        "tx_hash": "0x1234567890abcdef...",
        "block": 58921045
      }
    ]
  }
}
```

---

## 3. Canonical Leaf Hashing Mechanics

The cryptographic leaf hash $H_L$ is computed by sorting all non-null receipt keys alphabetically and computing the SHA-256 hash over the canonical compact JSON string:

$$H_L = \text{SHA256}(\text{CanonicalJSON}(\text{ReceiptData}))$$

Where canonical JSON enforces:
- Key sorting in lexicographical order.
- Compact separators (`:` with no whitespace, `,` with no whitespace).
- UTC ISO-8601 formatting for timestamps.

---

## 4. Merkle Batching & Multi-Chain Anchoring

1. **Merkle Tree Inclusion**: Individual leaf hashes $H_L$ are batched into a binary Merkle tree.
2. **Root Generation**: The Merkle root $R_M$ is signed with the authority's Ed25519 private key $K_{\text{priv}}$.
3. **Public Anchoring**: $R_M$ is anchored on Polygon Mainnet / Ethereum EVM smart contracts via state commitments.

---

## 5. 12-Stage Action Chain Lifecycle

The VAP protocol tracks the lifecycle of governed AI agents across 12 stages:

```
1. identity_registered      → Inactive → Reloading (registration)
2. authority_endorsed       → Reloading (validation)
3. emblem_issued            → Reloading (tokenization)
4. configuration_published  → Reloading (discovery)
5. discovery                → Reloading → Active (verification)
6. integrity_verified       → Active (health check)
7. monitoring               → Active (health check)
8. incident_reported        → Active → Unloading (anomaly)
9. forensic_package_generated→ Unloading (evidence)
10. protection_revoked       → Unloading → Inactive (termination)
11. protection_renewed       → Inactive → Reloading (continuation)
12. custom                   → Final state (custom action)
```

---

## 6. Agentic Execution Mesh Extensions (v3.5)

VAP v3.5 extends the baseline protocol to govern autonomous multi-agent networks operating across decentralized, zero-trust execution meshes.

### 6.1 Multi-Hop Delegation DAGs & Attenuated Tokens
When an agent delegates execution to sub-agents ($A \to B \to C$), each delegation hop is governed by an **Attenuated Delegation Token** (`DelegationToken`):
- **Monotonic Scope Attenuation**: Sub-agents MUST NOT escalate capabilities or transaction limits beyond delegator bounds ($\text{Scope}_{B} \subseteq \text{Scope}_{A}$).
- **Multi-Hop Chain Receipt Binding**: Every hop is recorded in a `multi_hop_chain` array within the `VAPReceipt`, binding hop indices, delegator IDs, delegate IDs, capability bounds, and hop signatures into the canonical Merkle leaf hash.

### 6.2 W3C Decentralized Identifiers (`did:mesh:<hex>`)
Agent identities in the mesh are represented as W3C Decentralized Identifiers under the `did:mesh` method.

### 6.3 5-Dimensional Vector Trust Scoring (`TrustVector5D`)
Agent reputation is evaluated across 5 explicit dimensions:
1. **Policy Compliance** ($w_1 = 0.30$): Violation rates & Guardian blocks.
2. **Security Posture** ($w_2 = 0.25$): Memory guard context poisoning & mTLS attestation.
3. **Output Quality** ($w_3 = 0.20$): Hallucination, toxicity, and user feedback scores.
4. **Resource Efficiency** ($w_4 = 0.10$): Token velocity & cost attribution.
5. **Collaboration Health** ($w_5 = 0.15$): Multi-agent arbitration & delegation success.

$$\text{Composite Score} = \sum_{i=1}^{5} w_i \cdot D_i$$

### 6.4 Zero-Knowledge Proofs & BBS+ Selective Disclosure
VAP receipts support privacy-preserving zero-knowledge proofs using Pedersen Commitments, ZK Range Proofs, and BBS+ Selective Disclosure.

### 6.5 Hybrid Post-Quantum Signatures (Ed25519 + ML-DSA-87)
VAP proofs support hybrid dual-signatures combining high-speed classical signatures with quantum-resistant signatures:
- **Classical**: Ed25519 64-byte signature.
- **Post-Quantum**: NIST FIPS 204 ML-DSA-87 (Dilithium 5) signature.
- Verification checks both `signature` and `pq_signature` when present, ensuring quantum immunity against Shor's algorithm.

### 6.6 Sub-5s Credential Revocation Propagation
Revocations propagate across mesh nodes in $< 5$ seconds using In-Memory Cache Lookups and Redis Pub/Sub broadcast channels.

### 6.7 Formal Semantic Governance & W3C Ontology Engine
VAP v3.5 binds syntactic VAP receipt JSON models to formal semantic knowledge graphs using W3C PROV-O & ODRL ontology alignment, JSON-LD context, SHACL shape validation, and SPARQL forensic queries for automated Article 71 EU AI Act compliance.

### 6.8 Agentic Commerce & Settlement
VAP v3.5 supports financial transactions between autonomous AI agents:
- **Terms Negotiation**: Canonical term hashing for proposals/counter-offers.
- **Trust-Weighted Escrow**: Parametric release of funds bound to VAP proof-of-delivery receipts.
- **x402 Micropayments**: Native HTTP 402 payment authorization headers and multi-chain stablecoin settlement (USDC, USDT, USDG).

---

## 7. Multi-Language SDK Specifications

All official SDKs (Python, TypeScript, Go, Rust) MUST adhere to the following interface contracts:

```
VeriLinkClient:
  - generate_receipt(action, decision, context, quality, cost, delegation_token, pq_keys) -> Receipt
  - verify_receipt(receipt) -> VerificationResponse
  - verify_signature(public_key, hash, signature) -> bool
  - verify_mldsa87_signature(pq_public_key, hash, pq_signature) -> bool
  - generate_range_proof(value, threshold) -> ZKSNARKProof
  - resolve_did(did_uri) -> DIDResolutionResult
```

---

## 8. Specification Status

| Component | Status |
|-----------|--------|
| Receipt Schema | ✅ Stable |
| Ed25519 Signatures | ✅ Stable |
| Merkle Batching | ✅ Stable |
| Blockchain Anchoring | ✅ Production-ready |
| Multi-Language SDKs | ⚠️ Python complete; others in progress |
| Post-Quantum Signatures | ✅ Production-ready (ML-DSA-87 hybrid) |
| Conformance Tests | 🔄 In development |
| IETF Standardization | 🔄 Drafting IETF Internet-Draft (SCITT alignment) |

---

## 9. Conformance

A VAP implementation MUST:
1. Generate receipts conforming to the schema in Section 2.
2. Sign receipts using Ed25519 as defined in Section 4.
3. Support Merkle tree batching as defined in Section 5.
4. Provide verification endpoints for third-party validation.

---

## 10. How to Contribute

To contribute:
1. Open an issue on GitHub.
2. Submit a pull request with proposed changes.
3. Join the community discussion forum.
