# VAP v3.5 Threat Model

This document outlines the security threats VAP v3.5 is designed to mitigate.

## Threat Matrix

| Attack | Protection |
| :--- | :--- |
| **Receipt modification** | Digital signature (`proof.signature` / `proof.pq_signature`) |
| **Receipt forgery** | Signer private key secrecy |
| **Replay** | `receipt_id` uniqueness, timestamp validation (`context.timestamp`), and nonce inclusion (if implemented in sub-protocol) |
| **Delegation escalation** | Monotonic scope attenuation ($\text{Scope}_{B} \subseteq \text{Scope}_{A}$) |
| **Delegation replay** | Delegation token binding to receipt hash |
| **Key compromise** | Key rotation mechanisms and timely revocation propagation |
| **Rogue signer** | Governance authority endorsement (`identity_registered` / `authority_endorsed`) |
| **Merkle proof substitution** | Merkle root signature by authority |
| **Anchor substitution** | Chain verification and state commitment validation |
| **Clock manipulation** | Timestamp policy, anchoring to blockchain, and threshold validation |
| **Model impersonation** | Model identity binding to receipt hash |
| **Prompt tampering** | Input commitment via hash |
| **Selective omission** | Sequence commitment in Merkle tree/chain commitment |
| **Compromised verifier** | Independent, offline verification capability |

## Security Considerations

1. **Replay Protection**: Implementers MUST ensure `receipt_id` is globally unique and validate `context.timestamp` against a trusted time source or anchor. Sequence numbers or nonces SHOULD be included in receipt metadata for high-frequency agents.
2. **Key Management**: Private keys used for signing receipts MUST be stored in a FIPS 140-2/3 Level 3 compliant Hardware Security Module (HSM).
3. **Canonicalization**: All implementations MUST use the canonical JSON algorithm defined in the specification to ensure signature verification stability.
4. **Post-Quantum Resilience**: While `ed25519_mldsa87_hybrid` provides quantum resistance, implementers must ensure that key storage and key exchange mechanisms are also quantum-secure.
