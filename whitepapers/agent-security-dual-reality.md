# Verifiable Authorization for Autonomous Agents
## A Cryptographic Execution Boundary for the Agentic Enterprise

**VeriLinkOS Architectural Briefing | August 2026 | Version 6.1**

---

## 1. Executive Summary
The emergence of autonomous AI agents has shifted cybersecurity from protecting static perimeters to governing dynamic, machine-scale actions. While agentic systems offer unprecedented capability, they introduce a fundamental security vulnerability: the gap between AI-generated intent and actual execution. This document outlines the core architectural proposition of the **Verifiable Action Protocol (VAP)**: **The Intent–Execution Separation Principle.** VAP turns authorization from an implicit decision into an explicit, portable, and cryptographically verifiable artifact that binds bounded authority to the exact action being executed.

---

## 2. The Authorization Gap: Autonomous Agents Expose a New Security Boundary
Enterprises are deploying autonomous AI agents that can access APIs, execute financial transactions, modify production systems, and negotiate contracts. However, the authorization model for these agents is broken. Enterprises are currently choosing between:
1. **Unrestricted Authority:** "Just-in-case" permissions create catastrophic risk.
2. **No Authority Model:** Governance is deferred, creating an unmanageable security deficit.

**Existing enterprise controls provide identity, permissions, policy enforcement, and monitoring. The emerging gap is how to carry bounded authority into autonomous execution and independently verify that a consequential action remains within that authority.**

---

## 3. The Thesis: The Intent–Execution Separation Principle
The central proposition of the **Verifiable Action Protocol (VAP)** is:

> **Agent-generated intent and authorized execution must be cryptographically separable.**

> **A compromised agent should be capable of generating malicious intent without possessing the authority required to execute it.**

In a VAP-enabled architecture, an agent proposes an action, but the execution boundary is solely responsible for determining if that action is authorized, satisfies its constraints, and carries valid, signed authorization. VAP does not require the agent to be trusted with unrestricted authority. Instead, it limits the security consequence of agent compromise by making execution conditional on independently verifiable authorization.

---

## 4. Why Conventional Authorization is Insufficient
Conventional authorization (`Identity → Role → Permission`) is centered on whether a principal is permitted to invoke an operation. Agentic systems introduce a new requirement: **consequential actions must carry bounded, verifiable authority through the execution path.**

| Feature | Conventional IAM | VeriLinkOS (VAP) |
| :--- | :--- | :--- |
| **Authorization Unit** | Principal / resource / operation | Bounded action (Contextual) |
| **Authority** | Permission (Static/Dynamic) | Signed Authorization Artifact |
| **Enforcement** | Policy decision (PDP/PEP) | Action-level verification (Match) |
| **Provenance** | Platform records (Logs) | Cryptographically bound evidence |
| **Verification** | Platform-dependent | Independent / Cross-Domain |
| **Execution** | Request permitted | Request must satisfy authorization artifact |

Conventional authorization can absolutely constrain individual requests; however, **VAP introduces a different security property: it makes bounded authority an explicit, portable artifact that can be independently verified at the execution boundary.**

---

## 5. The VAP Primitive: What is a PERMIT?
A PERMIT is not an instruction to execute; it is a **signed statement of bounded authority** that an execution boundary can independently verify against a proposed action.

### Anatomy of a PERMIT Artifact
```json
{
  "permit_id": "perm-abc123",
  "issuer": "did:mesh:governance-authority",
  "agent_id": "did:mesh:agent-xyz",
  "scope": {
    "action_type": "financial_transfer",
    "constraints": {
      "max_amount": 200.00,
      "currency": "EUR",
      "allowed_recipients_hash": ["sha256:7a3f..."]
    }
  },
  "policy_binding": {
    "policy_id": "insurance_v2.1",
    "policy_hash": "sha256:d8b9..."
  },
  "validity": {
    "issued_at": "2026-08-30T10:00:00Z",
    "expires_at": "2026-08-30T10:15:00Z"
  },
  "nonce": "n-12345",
  "signature": "ed25519:..."
}
```

### The "MAY-DID-MATCH" Execution Flow
```text
        UNTRUSTED / VARIABLE
             AGENT DOMAIN

┌─────────────────────────────┐
│       AI Agent / Model      │
│                             │
│  reasoning • planning       │
│  tool selection • intent    │
└──────────────┬──────────────┘
               │ REQUEST
               ▼
════════════════════════════════════
        VERIFIABLE EXECUTION
              BOUNDARY (MATCH)
════════════════════════════════════
               │
        PERMIT (MAY) VERIFIED
        CONSTRAINTS PASS
        POLICY MATCHES
        REPLAY CHECK PASSES
               │
               ▼
┌─────────────────────────────┐
│      Execution Systems      │
│ APIs • MCP • Payments • Data│
└──────────────┬──────────────┘
```

---

## 6. Who Needs This

| Stakeholder | The Problem | Why VAP Matters |
|-------------|-------------|-----------------|
| **CISOs** | Can't prove agents are acting within bounds | Cryptographic proof of enforcement |
| **Compliance Officers** | Audit trails are manual and unreliable | Immutable, independently verifiable receipts |
| **AI Engineers** | Deploying agents is risky without guardrails | Fail-closed enforcement, not monitoring |
| **Risk Officers** | Can't quantify or insure AI risk | Verifiable evidence for underwriters |

---

## 7. End-to-End Example: Autonomous Procurement Agent
**Authorized State:** Agent is authorized for payments up to €200 to `Vendor X` under `Policy v2.1`.
**Attack Scenario:** Agent is compromised and proposes: `Buy €4,000 from Vendor Y`.

**Enforcement (The MATCH Boundary):**
*   **Amount Constraint:** ✗ (`€4,000 > €200`)
*   **Recipient Match:** ✗

**Result:** **🚫 EXECUTION DENIED (No settlement generated).**
*The agent was compromised, but its execution authority was not.*

---

## 8. Threat Model & Defensive Invariants
VAP enforces security invariants at the execution boundary.

| Attack Vector | Defensive Invariant | Protection Strategy |
| :--- | :--- | :--- |
| **Prompt Injection** | **Constraint Enforcement** | Gateway rejects actions violating PERMIT limits. |
| **Privilege Escalation** | **Action-Bound Authority** | Agents possess authority for *this* action, not *all* actions. |
| **Policy Substitution** | **Policy Hash Binding** | Execution rejected if policy binding is tampered with. |
| **Replay Attack** | **Nonce Tracking** | Gateway rejects previously consumed permit identifiers. |
| **Log Alteration** | **Immutable Receipts** | Evidence is independently verifiable. |

---

## 9. Strategic Differentiation

| Feature | Conventional IAM | VeriLinkOS (VAP) |
| :--- | :--- | :--- |
| **Enforcement** | Passive monitoring | Fail-closed runtime gate |
| **Provenance** | Internal platform logs | Cryptographically bound receipts |
| **Verification** | Platform-dependent | Independent / Cross-domain |
| **Authoritative Unit** | Identity/Role | Bounded action artifact |

**Competitors are building visibility. VAP is building verification.**

---

## 10. What Verifiable Authorization Enables
With VAP, enterprises can:
- **Delegate safely:** Agents act autonomously without unrestricted authority.
- **Prove compliance:** Cryptographic receipts provide audit-ready evidence.
- **Secure insurance:** Verifiable governance makes AI risk insurable.
- **Scale confidently:** No need to micro-manage every agent action.
- **Recover from compromise:** A compromised agent cannot execute unauthorized actions.

---

## 11. Open Standard
The VAP specification is open and vendor-neutral. Any enterprise or platform can implement it. VeriLinkOS provides the reference implementation.

---

## 12. Next Steps
1. **Evaluate VAP for your organization.** Request a technical briefing.
2. **Run a pilot.** Test VAP with a specific agent use case.
3. **Shape the standard.** Contribute to the open VAP specification.
4. **Secure your agentic future.** Don't wait for a breach to prove the need.

---

## 13. Glossary
- **PERMIT (MAY):** A signed statement of bounded authority.
- **REQUEST (DID):** A machine-readable proposal by an agent to perform an action.
- **SETTLEMENT (DID):** The cryptographic proof that an action was executed.
- **Execution Boundary:** The trusted layer where authorization is verified.
- **Action-Bound Authority:** Authority that is tied to a specific action, not a principal.

---
*Architectural Specification for the VeriLinkOS Agentic Framework.*
