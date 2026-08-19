# Verifiable Action Protocol (VAP) v3.0 Specification
**Open Specification for Cryptographic AI Governance & Action Provenance**

---

## 1. Abstract
The Verifiable Action Protocol (VAP) v3.0 is an open, vendor-neutral specification for generating, signing, batching, and verifying cryptographic proof of AI decisions, tool executions, quality evaluations, token costs, and compliance filings. VAP creates tamper-evident receipts that bind model identity, input context, policy enforcement, quality scores, token costs, and Merkle tree roots into Ed25519-signed digital evidence packages.

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
    "scheme": "ed25519",
    "signature": "3a4b5c...6d7e8f",
    "signer_public_key": "a1b2c3...d4e5f6",
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

1. `identity_registered`: Initial cryptographic registration of the agent or model.
2. `authority_endorsed`: Endorsement by organizational or regulatory authority.
3. `emblem_issued`: Generation of verifiable digital emblem.
4. `configuration_published`: Configuration published to DNS TXT / well-known discovery.
5. `discovery`: Discovery via `/.well-known/verilink/`.
6. `integrity_verified`: Runtime verification of model checkpoint or tool output.
7. `monitoring`: Continuous real-time Guardian monitoring.
8. `incident_reported`: Security or policy incident logged.
9. `forensic_package_generated`: Court-admissible evidence package created.
10. `protection_revoked`: Administrative revocation of protection.
11. `protection_renewed`: Automated lifecycle extension.
12. `custom`: Domain-specific custom action provenance.

---

## 6. Agentic Execution Mesh Extensions (v3.5)

VAP v3.5 extends the baseline protocol to govern autonomous multi-agent networks operating across decentralized, zero-trust execution meshes.

---

### 6.1 Multi-Hop Delegation DAGs & Attenuated Tokens
When an agent delegates execution to sub-agents ($A \to B \to C$), each delegation hop is governed by an **Attenuated Delegation Token** (`DelegationToken`):
- **Monotonic Scope Attenuation**: Sub-agents MUST NOT escalate capabilities or transaction limits beyond delegator bounds ($\text{Scope}_{B} \subseteq \text{Scope}_{A}$).
- **Multi-Hop Chain Receipt Binding**: Every hop is recorded in `multi_hop_chain` array within the `VAPReceipt`, binding hop indices, delegator IDs, delegate IDs, capability bounds, and hop signatures into the canonical Merkle leaf hash.

---

### 6.2 W3C Decentralized Identifiers (`did:mesh:<hex>`)
Agent identities in the mesh are represented as W3C Decentralized Identifiers under the `did:mesh` method:
- **Canonical Format**: `did:mesh:<64-char-hex-fingerprint>` derived from the SHA-256 fingerprint of the agent's Ed25519 public key.
- **DID Document Schema**:
  ```json
  {
    "@context": [
      "https://www.w3.org/ns/did/v1",
      "https://w3id.org/security/suites/ed25519-2020/v1"
    ],
    "id": "did:mesh:87a9f56d0f3a4f47b2696c18fcc5ab70e72f18453bee31d0535ccb44276d92f6",
    "verificationMethod": [
      {
        "id": "did:mesh:87a9f56d...#keys-1",
        "type": "Ed25519VerificationKey2020",
        "controller": "did:mesh:87a9f56d...",
        "publicKeyHex": "a1b2c3..."
      }
    ],
    "service": [
      {
        "id": "did:mesh:87a9f56d...#vap-endpoint",
        "type": "VAPMeshService",
        "serviceEndpoint": "https://node.verilink.mesh/v1/vap"
      }
    ]
  }
  ```
- **W3C Resolver API**: Endpoint `GET /v1/did/{did_uri}` resolves any `did:mesh:<hex>` to its JSON-LD document.

---

### 6.3 5-Dimensional Vector Trust Scoring (`TrustVector5D`)
Agent reputation is evaluated across 5 explicit dimensions:
1. **Policy Compliance** ($w_1 = 0.30$): Violation rates & Guardian blocks.
2. **Security Posture** ($w_2 = 0.25$): Memory guard context poisoning & mTLS attestation.
3. **Output Quality** ($w_3 = 0.20$): Hallucination, toxicity, and user feedback scores.
4. **Resource Efficiency** ($w_4 = 0.10$): Token velocity & cost attribution.
5. **Collaboration Health** ($w_5 = 0.15$): Multi-agent arbitration & delegation success.

$$\text{Composite Score} = \sum_{i=1}^{5} w_i \cdot D_i$$

- **P2P CRDT Convergence**: Node trust vectors merge asynchronously using Vector Clock Max rules and Last-Write-Wins (LWW) component convergence across air-gapped networks.

---

### 6.4 Zero-Knowledge Proofs & BBS+ Selective Disclosure
VAP receipts support privacy-preserving zero-knowledge proofs:
- **Pedersen Commitments**: Private values $v$ (e.g., transaction amounts or model weights) are committed as $C = \text{SHA256}(v \parallel r \parallel \text{param})$ hiding raw data.
- **ZK Range Proofs**: Asserts $v \le \text{threshold}$ without disclosing $v$.
- **BBS+ Selective Disclosure**: Discloses chosen claims (e.g. `autonomy_level: 3`) while binding hidden claims to a Merkle Root ($R_{\text{hidden}}$).

---

### 6.5 Hybrid Post-Quantum Signatures (Ed25519 + ML-DSA-87)
VAP proofs support hybrid dual-signatures combining high-speed classical signatures with quantum-resistant signatures:
- **Classical**: Ed25519 64-byte signature.
- **Post-Quantum**: NIST FIPS 204 ML-DSA-87 (Dilithium 5) signature.
- Verification checks both `signature` and `pq_signature` when present, ensuring quantum immunity against Shor's algorithm.

---

### 6.6 Sub-5s Credential Revocation Propagation
Credential, key, and capability revocations propagate across mesh nodes in $< 5$ seconds:
- **In-Memory Cache Lookup**: $< 1\text{ms}$ instantaneous set check during Guardian policy enforcement.
- **Redis Pub/Sub Channel**: `verilink:mesh:revocation` channel broadcasts `RevocationNotice` payloads across distributed nodes in $< 50\text{ms}$.

---

### 6.8 Formal Semantic Governance & W3C Ontology Engine
VAP v3.5 binds syntactic VAP receipt JSON models to formal semantic knowledge graphs:
- **W3C PROV-O & ODRL Ontology Alignment**: Maps VAP receipts (`verilink:VAPReceipt`), agents (`verilink:Agent`), and actions (`verilink:AgentAction`) to `prov:Entity`, `prov:Activity`, `prov:Agent`, and `odrl:Permission` / `odrl:Constraint`.
- **JSON-LD Context (`verilink.jsonld`)**: Enables canonical RDF triple serialization (`@context: https://verilink.ai/v1/context.jsonld`).
- **SHACL Shape Validation (`SHACLPolicyEngine`)**: Machine-executable SHACL constraint validation evaluating graph invariants across multi-agent delegation chains.
- **SPARQL Forensic Queries (`SPARQLQueryEngine`)**: Enables SPARQL graph queries over stored VAP receipt RDF triplestores via `POST /v1/sparql/query` for forensic analysis and automated Article 71 EU AI Act compliance filings.

---

### 6.9 Agentic Commerce, Terms Negotiation, & Trust-Weighted Escrow
VAP v3.5 extends the protocol to govern financial transactions and escrow settlements between autonomous AI agents:
- **Terms Proposal & Agreement Hashing (`AgentNegotiationEngine`)**: Standardizes commerce proposals, counter-offers, and canonical term hashing.
- **Trust-Weighted Escrow Contracts (`AgentEscrowContract`)**: Locks buyer funds and verifies seller VAP proof-of-delivery receipts before releasing funds.
- **ZK Range Proof Settlement**: Binds ZK range proofs asserting payout threshold compliance to escrow release transactions.
- **REST Protocol Endpoints**: `/v1/commerce/negotiate/propose`, `/v1/commerce/negotiate/accept`, `/v1/commerce/escrow/create`, `/v1/commerce/escrow/deliver`, `/v1/commerce/escrow/release`.

---

### 6.10 x402 Micropayments & Multi-Chain Stablecoin Settlement
VAP v3.5 natively supports HTTP 402 / x402 payment authorization headers and stablecoin settlement:
- **HTTP 402 Response Headers (`X402SettlementEngine`)**: Standardizes `WWW-Authenticate: X402 realm="..."`, `X402-Payment-Intent`, and `X402-Terms-Hash`.
- **Multi-Chain On-Chain Verification**: Verifies USDC, USDT, and USDG transfer proofs across Polygon, Base, Ethereum, and Solana.
- **REST Protocol Endpoints**: `/v1/commerce/x402/intent`, `/v1/commerce/x402/settle`, `/v1/commerce/x402/headers/{intent_id}`.

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


## Specification Status

| Component | Status |
|-----------|--------|
| Receipt Schema | ✅ Stable |
| Ed25519 Signatures | ✅ Stable |
| Merkle Batching | ✅ Stable |
| Blockchain Anchoring | ✅ Production-ready |
| Multi-Language SDKs | ⚠️ Python complete; others in progress |
| Post-Quantum Signatures | 🔬 Research phase |
| Conformance Tests | 🔄 In development |
| IETF Standardization | 🔄 Under discussion |

**This is a live specification.** Version 3.0 represents the current design. Implementation feedback is welcome.


## Conformance

A VAP implementation MUST:

1. Generate receipts conforming to the schema in Section 2
2. Sign receipts using Ed25519 as defined in Section 4
3. Support Merkle tree batching as defined in Section 5
4. Provide verification endpoints for third-party validation

A VAP implementation SHOULD:

1. Support blockchain anchoring (Polygon/Ethereum)
2. Provide multi-language SDKs
3. Support 12-stage Action Chain lifecycle

The following implementations are known to be conformant:
- VeriLinkOS (reference implementation)
- [Third-party implementations TBD]


## How to Contribute

This specification is open for community input. Contributions are welcome in the following areas:

1. **Protocol Design**: Feedback on the receipt schema, signing process, and verification flow
2. **Implementations**: SDKs in additional languages
3. **Conformance Tests**: Additional test vectors and test suites
4. **Use Cases**: Real-world deployment scenarios

To contribute:
1. Open an issue on GitHub
2. Submit a pull request with proposed changes
3. Join the community discussion forum


