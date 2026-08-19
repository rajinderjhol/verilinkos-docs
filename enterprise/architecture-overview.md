# VeriLinkOS Architecture Overview

VeriLinkOS is the **Sovereign Autonomy Engine for the Agentic Web**. It automates the entire agentic lifecycle—Discover, Govern, Heal, and Settle—with cryptographic certainty, providing kernel-level enforcement and court-admissible provenance for AI workforces and digital assets.

## 🏛️ 3-Layer Modular Architecture

VeriLinkOS follows a clean 3-Layer separation of concerns:

1.  **Layer 1: Endpoint Controllers**: Modular FastAPI APIRouters handling HTTP transport, OpenAPI schemas, and request parsing.
2.  **Layer 2: Domain Services**: Pure Python business logic including `AssetProtectionService`, `GuardianLayer`, `PolicyEngine`, and `AIBOMGenerator`.
3.  **Layer 3: Infrastructure Core**: Consolidated database engines, ORM domain models, JWT/RBAC dependencies, cryptographic keys, and configuration.

## 🏗️ Core Architecture Layers

### 1. Guardian Layer (The Execution Control Plane)
Acts as the "Kernel" of the OS, intercepting all AI intents before they are executed. It implements fail-closed logic: if instruction injection or policy violations are detected, execution is terminated immediately.

### 2. Constitutional AI Layer
Provides ethical and legal guardrails for AI behavior through a modular `MetaRuleSet`.

### 3. Policy Engine Layer
Handles dynamic, organization-specific policy evaluation using OPA/Rego and high-performance Redis caching.

### 4. Cryptographic Core
Generates Verifiable Action Protocol (VAP) receipts, Merkle tree batching, and blockchain anchoring for tamper-evident evidence.

### 5. Asset Protection Layer (Digital Title Infrastructure)
Provides a cryptographically verifiable **Chain of Custody** for digital assets and tokenized Real-World Assets (RWAs). It links every event into an **Action Chain**, creating a legal and technical record.

## 🔌 Sovereign Governance Switchboard (MCP)
100% compliant with the Model Context Protocol (MCP) 2026-07-28 specification, VeriLinkOS acts as a gateway for any MCP client (Cursor, Claude, AutoGen) to enforce governance and settlement.

## 🌐 Agentic Execution Mesh
Supports multi-hop delegation, SPIFFE/SVID mutual TLS, and 5D vector trust scoring across organizational boundaries.
