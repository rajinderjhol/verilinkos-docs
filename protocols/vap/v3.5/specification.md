# Verifiable Action Protocol (VAP) v3.5 Specification
**Open Specification for Cryptographic AI Governance & Action Provenance**

---

## 1. Abstract
The Verifiable Action Protocol (VAP) v3.5 is an open, vendor-neutral specification for generating, signing, batching, and verifying cryptographic proof of AI decisions, tool executions, quality evaluations, token costs, and compliance filings. VAP creates tamper-evident receipts that bind model identity, input context, policy enforcement, quality scores, token costs, and Merkle tree roots into Ed25519-signed digital evidence packages. VAP v3.5 introduces native support for hybrid post-quantum signatures (ML-DSA-87) to ensure long-term integrity. It is designed with alignment to IETF SCITT (Supply Chain Integrity, Transparency, and Trust) standards and is intended for future Internet-Draft submission.

---

## 2. Receipt Data Model
An implementation MUST validate against the normative schema provided in `schemas/vap-receipt-v3.5.schema.json`.

---

## 3. Normative Cryptographic Specification

### 3.1 Canonicalization & Hashing
To ensure deterministic verification across independent implementations, receipts MUST use the following canonicalization:
1. **Key Sorting**: All JSON keys MUST be sorted lexicographically (ASCII order).
2. **Compact JSON**: No whitespace allowed between keys, values, or structure separators (e.g., `{"a":1,"b":2}`).
3. **Number Representation**: Numbers MUST NOT have trailing zeros. Floating-point numbers MUST follow IEEE 754.
4. **Timestamp Normalization**: Timestamps MUST be encoded in UTC ISO-8601 format (`YYYY-MM-DDTHH:MM:SSZ`).
5. **UTF-8 Encoding**: All strings MUST be UTF-8 encoded.

### 3.2 Signing
The signature covers the canonicalized JSON representation of the `action`, `decision`, `context`, `quality_scores`, and `cost` blocks.
- **Algorithm**: `ed25519` for classical signatures; `ed25519_mldsa87_hybrid` for quantum-resistant signatures.
- **Encoding**: Hexadecimal encoding for signature outputs.

---

## 4. VAP Receipt Verification Algorithm
An implementation MUST execute the following steps to verify a receipt:

1. **Parse receipt**: Deserialize JSON.
2. **Validate schema**: Validate against `vap-receipt-v3.5.schema.json`.
3. **Canonicalize**: Reconstruct canonical payload based on Section 3.
4. **Calculate Hash**: SHA-256 over canonical payload.
5. **Verify Classical Signature**: Verify Ed25519 against `signer_public_key`.
6. **Verify PQ Signature**: (If present) Verify ML-DSA-87 against `pq_public_key`.
7. **Validate Merkle inclusion proof**: Verify inclusion of leaf hash in `merkle_root`.
8. **Verify Anchor**: If present, resolve blockchain anchor commitment.
9. **Evaluate Policy/Timestamp**: Validate `context.timestamp` and `policy_id` validity.
10. **Check Revocation**: Verify agent key is not in the revocation list.
11. **Return Result**: Return `valid: true` or `valid: false` with specific `errors` code.

---

## 5. Threat Model & Security
Implementations MUST refer to `security/threat-model.md` for comprehensive protection against replay attacks, key compromise, and manipulation.

---

## 6. Lifecycle & Mesh Extensions (v3.5)
*(Refers to previous specification content regarding 12-stage lifecycle, delegation DAGs, trust scoring, ZK, and commerce extensions.)*

---

## 7. Conformance & Test Vectors
Implementations MUST pass the conformance tests defined in the `conformance/` directory, including valid and invalid JSON vectors.

---

## 8. How to Contribute
1. Open an issue on GitHub.
2. Submit a pull request with proposed changes.
3. Join the community discussion forum.
