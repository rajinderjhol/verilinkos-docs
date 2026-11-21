# VeriLinkOS: The Control Plane for Agentic Systems

## Abstract

The emergence of autonomous AI agents as first-class actors in enterprise systems creates a fundamental governance gap: how do we control, track, and prove the actions of non-human agents that operate at scale, make decisions independently, and transact with other autonomous systems?

We present VeriLinkOS, a control plane with a shipped core that implements the formal principles of revertible effects and reactive coeffects—previously established in the Cordis framework—specifically for the domain of autonomous AI agents. VeriLinkOS provides five integrated layers: cryptographic identity (W3C-DID-shaped local methods + Ed25519), policy-based authorization (CEL, optional external OPA/Rego), fail-closed runtime enforcement (Guardian), verifiable action receipts (VAP with Merkle-tree commitments and optional anchoring), and agent-to-agent settlement (x402 micropayments + escrow).

We describe the system architecture, formal mapping to revertible effect theory, implementation in a production-ready Python/FastAPI codebase with 894 passing tests (as of 2026-11-21), and validation through PersonaVault, a sovereign decision engine built on VeriLinkOS.

**Keywords**: AI Governance, Autonomous Agents, Revertible Effects, Reactive Coeffects, Cryptographic Proof, Agentic Systems
