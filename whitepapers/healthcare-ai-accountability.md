# Verifiable AI Accountability for Swiss Healthcare

## Cryptographic Evidence for Federated AI Ecosystems

**Document Type:** Technical Reference Architecture & Pilot Proposal  
**Date:** August 2026  
**Version:** 2.1  
**Author:** Rajinder Jhol, AI Governance Architect  
**Contact:** rajinderjhol @gmail.com

---

## Executive Summary

Switzerland is building the future of healthcare AI—and it is building it together. The CHF 18.9 million NAIPO initiative unites all Swiss university hospitals in a federated AI network for precision oncology (Debiopharm, 2025a). The SPHN national data infrastructure continues to expand. The HLS Blueprint calls for a "national drive for the integration of AI and data science tools" in the Health and Life Sciences cluster (National ORD Strategy Council, 2024).

Yet a critical governance gap remains.

Swiss clinicians are already using AI—69.3% have used large language models, 56.9% for medical purposes—but only 6% have workplace guidelines (Egli et al., 2025). A survey of Swiss ophthalmologists found that while 80% agree AI can support diagnostics, only 20.8% are using it clinically, with barriers including "insufficient institutional infrastructure" and "lack of AI-related guidelines" (Janssen et al., 2025). This is not a technical gap—it is a governance gap.

The regulatory context is urgent. The EU AI Act applies extraterritorially to Swiss providers and deployers (Lexology, 2026). Switzerland's sector-specific approach has created a "considerable gap in Swiss law when it comes to AI, particularly in medicine" (Russ et al., 2024). The Swiss Federal Council has proposed aligning with the Council of Europe's AI Convention rather than implementing a comprehensive AI law, meaning Swiss hospitals must navigate EU requirements while awaiting domestic clarity (SWI swissinfo.ch, 2024).

This paper proposes a **reference architecture for verifiable AI accountability**—a technical framework that generates independently verifiable evidence of AI decisions. It is designed to operate across institutional boundaries, making AI governance verifiable without requiring trust in any single party. By addressing the documented barriers of "insufficient institutional infrastructure," "lack of AI-related guidelines," and "regulatory uncertainty," this architecture aims to accelerate AI adoption by enabling healthcare institutions to deploy AI with confidence (Janssen et al., 2025; Russ et al., 2024).

The architecture is independent of any specific trust infrastructure. It can be deployed standalone or complement existing infrastructure where present (e.g., SPHN, HIN, Vereign's Verimesh) (Vereign, 2026). The key principle is interoperability, not dependency.

**A pilot implementation is available for technical evaluation.** VeriLinkOS, a cloud API service implementing this architecture, is available for research collaboration agreements. Contact rajinderjhol @gmail.com to request a pilot.

---

## Table of Contents

1. [Introduction](#1-introduction)
2. [The Swiss Healthcare AI Ecosystem](#2-the-swiss-healthcare-ai-ecosystem)
3. [The Regulatory Reality](#3-the-regulatory-reality)
4. [The Evidence Gap](#4-the-evidence-gap)
5. [Proposed Architecture](#5-proposed-architecture)
6. [Relationship to Existing Trust Infrastructure](#6-relationship-to-existing-trust-infrastructure)
7. [Strategic Opportunities in Swiss Healthcare](#7-strategic-opportunities-in-swiss-healthcare)
8. [Pilot Pathway](#8-pilot-pathway)
9. [Conclusion](#9-conclusion)
10. [References](#10-references)
11. [Annex A: Receipt Schema Specification](#11-annex-a-receipt-schema-specification)
12. [Annex B: Threat Model and Security Considerations](#12-annex-b-threat-model-and-security-considerations)

---

## 1. Introduction

### 1.1 What This Document Is and Is Not

**This document is:**
- A reference architecture for verifiable AI governance in healthcare—a technical framework for generating independently verifiable evidence of AI decisions
- A technical contribution to the ongoing multistakeholder dialogue on AI governance in healthcare, offered for discussion and evaluation
- A vendor-neutral specification designed to be implementable using open standards and complementary to existing Swiss healthcare infrastructure

**This document is not:**
- A commercial product proposal
- A binding standard or regulatory requirement
- A complete governance system—it addresses the evidence layer, not policy definition or institutional governance

### 1.2 The Accountability Challenge

Healthcare institutions face a fundamental governance challenge. A policy defines what should happen. A log records that something happened. An attestation declares compliance. None, by themselves, constitute independently verifiable proof that controls were enforced at the time an action occurred.

The problem becomes acute in cross-institutional settings. When AI decisions are made across federated networks (NAIPO, SPHN), when models are trained collaboratively, when care is coordinated across institutions—traditional audit trails are institution-bound and operator-controlled.

**Concrete Failure Scenario:**

Hospital A trains an AI model on 10,000 oncology cases. Hospital B deploys the same model. Hospital C audits outcomes across the network. A regulator investigates an incident 18 months later.

Without a common evidence architecture, each hospital presents its own logs—none independently verifiable, none portable, none designed for cross-institutional audit. The regulator cannot determine what actually happened.

### 1.3 Why Federated AI Makes the Problem Worse

In a federated system, the question "whose logs do we trust?" has no good answer:

| Option | Problem |
|--------|---------|
| Trust each hospital's logs | Non-independent; each is a party to the audit |
| Centralise logging | Contravenes federated principles and data sovereignty |
| Rely on vendor logs | Vendors are not impartial |

A verifiable trust architecture provides the fourth option: **independent, cryptographic verification that works across institutional boundaries.**

---

## 2. The Swiss Healthcare AI Ecosystem

Swiss healthcare is building federated, cross-institutional AI ecosystems. These collaborations create unique governance challenges that a verifiable trust architecture is designed to solve.

### 2.1 NAIPO: National AI Initiative for Precision Oncology

| Aspect | Detail |
|--------|--------|
| **Scale** | CHF 18.9 million, 4-year timeline (Debiopharm, 2025a) |
| **Participants** | EPFL, ETH, SDSC, CSCS, University Hospitals (Basel, Bern, Geneva, Zurich), Debiopharm, Roche, etc. (Debiopharm, 2025a) |
| **Mission** | Build a secure, Swiss-hosted infrastructure for AI-enhanced precision oncology (Debiopharm, 2025a) |

**Key Quote:** *"We are creating a secure and federated system that allows collaboration across institutions without compromising confidentiality. Trust and transparency will be built into the design."* — Nora Toussaint, SDSC (Debiopharm, 2025a)

### 2.2 SPHN: Swiss Personalized Health Network

| Aspect | Detail |
|--------|--------|
| **Scale** | National infrastructure with federal mandate 2025-2028 (SPHN, 2025) |
| **Mission** | Make health data interoperable and shareable for research across Switzerland (SPHN, 2025) |

### 2.3 HLS Blueprint

The HLS Blueprint identifies AI integration as a strategic priority for Switzerland (National ORD Strategy Council, 2024).

**Key Requirements Identified:**
- Regulatory frameworks must ensure safety, efficacy, and quality.
- AI algorithms need rigorous scrutiny and benchmarking.
- Standardised evaluation protocols are required to ensure consistency.

### 2.4 Participatory Assessment at CHUV

A study at Lausanne University Hospital (CHUV) assessed LLM use-cases with 30 stakeholders, identifying specific issues regarding ethics and regulatory compliance (Kulynych et al., 2024).

---

## 3. The Regulatory Reality

### 3.1 The Regulatory "Lasagne"

AI-enabled medical devices must navigate overlapping regulatory frameworks (Weber et al., 2026):
- **MDR/IVDR**: Clinical safety and device performance.
- **EU AI Act**: Algorithmic transparency, bias, and fundamental rights.
- **GDPR**: Data protection and privacy.

### 3.2 The EU AI Act Applies Extraterritorially

Swiss-based companies are directly impacted. The AI Act requires compliance if systems are placed on the EU market or generate output used in the EU (Lexology, 2026).

### 3.3 Updated Compliance Timeline

| Obligation | Deadline |
|------------|----------|
| Transparency (Article 50) | 2 August 2026 |
| High-risk AI – Annex III | 2 December 2027 |
| High-risk AI – Annex I | 2 August 2028 |

---

## 4. The Evidence Gap

### 4.1 The Challenge

Traditional audit trails are institution-bound, operator-controlled, and not independently verifiable.

### 4.2 Why Cryptographic Evidence Matters

**The VeriLinkOS Approach:** Cryptographic receipts provide tamper-evident, independently verifiable evidence of AI decisions. They are:
- Cryptographically signed and immutable
- Portable across systems
- Independently verifiable by any third party using only public keys

---

## 5. Proposed Architecture

### 5.1 The Five Layers

| Layer | Function |
|-------|----------|
| **1. Cryptographic Receipts** | Prove AI action fidelity |
| **2. Trust Passports** | Verify agent identity/reputation |
| **3. Reputation** | Track agent performance |
| **4. Policy** | Enforce runtime rules |
| **5. Trust Intelligence** | Detect risks predictively |

### 5.2 Data Sovereignty and Privacy

The architecture is privacy-preserving by design:
- **Data Minimisation**: Stores only cryptographic proofs—no patient data.
- **Data Sovereignty**: Patient data remains within institutional control.
- **No Vendor Lock-in**: Receipts are machine-readable and vendor-neutral.

---

## 6. Relationship to Existing Trust Infrastructure

The architecture is **vendor-neutral** and interoperable. It can complement existing infrastructure like Vereign's Verimesh (Vereign, 2026) or SPHN data services.

---

## 7. Strategic Opportunities in Swiss Healthcare

Institutions like **USB/MAIVAN**, **Inselspital**, **CHUV**, and **HUG Geneva** are uniquely positioned to pilot this architecture, addressing their documented AI governance and regulatory compliance needs (MAIVAN, 2026; Dennstädt et al., 2026; Kulynych et al., 2024; Debiopharm, 2025a).

---

## 8. Pilot Pathway

1. **Technical Evaluation (Immediate)**: API trial and sandbox environment.
2. **Focused Pilot (3-6 months)**: Integration with one existing clinical AI system.
3. **Enterprise Deployment (6-12 months)**: Full institution-wide implementation.

**Contact rajinderjhol @gmail.com to request a pilot.**

---

## 9. Conclusion

VeriLinkOS offers a technical framework to transform AI governance from documented policy to cryptographically provable enforcement. This is the missing infrastructure for trusted, sovereign, and compliant healthcare AI in Switzerland.

---

## 10. References

[1] Debiopharm. (2025a). Debiopharm joins the working group of the NAIPO initiative.  
[2] Dennstädt F, et al. (2026). The EU AI Act: Implications for Healthcare. Frontiers in Digital Health.  
[3] Egli SB, et al. (2025). Use, knowledge and perception of large language models in clinical practice. BMJ Health Care Inform.  
[4] Janssen L, et al. (2025). Artificial Intelligence in Ophthalmology. J Clin Med.  
[5] Kulynych B, et al. (2024). Participatory Assessment of LLM Applications. arXiv.  
[6] Lexology. (2026). The EU AI Act and Medical Devices.  
[7] MAIVAN. (2026). About MAIVAN.  
[8] Mishcon de Reya. (2026). EU AI Act Omnibus Agreement.  
[9] National ORD Strategy Council. (2024). HLS Blueprint.  
[10] Russ C, et al. (2024). Effects of MDR on ML Solutions in Swiss Hospitals. HMD.  
[11] SPHN. (2025). Swiss Personalized Health Network ensures continuity.  
[12] SWI swissinfo.ch. (2024). AI regulation in Switzerland.  
[13] Vereign. (2026). Distributed Trust in Regulated Sectors.  
[14] Weber C, et al. (2026). Medtech trifft KI-Regulierung. Medinside.  

---

## 11. Annex A: Receipt Schema Specification

```json
{
  "type": "object",
  "required": ["receipt_id", "version", "timestamp", "input", "decision", "context", "signature"],
  "properties": {
    "receipt_id": { "type": "string" },
    "version": { "type": "string" },
    "timestamp": { "type": "string", "format": "date-time" },
    "input": {
      "type": "object",
      "required": ["hash", "schema"],
      "properties": {
        "hash": { "type": "string" },
        "schema": { "type": "string" }
      }
    },
    "decision": {
      "type": "object",
      "required": ["type", "value"],
      "properties": {
        "type": { "type": "string" },
        "value": { "type": "string" }
      }
    },
    "signature": {
      "type": "object",
      "required": ["algorithm", "public_key", "signature"],
      "properties": {
        "algorithm": { "type": "string" },
        "public_key": { "type": "string" },
        "signature": { "type": "string" }
      }
    }
  }
}
```

---

## 12. Annex B: Threat Model and Security Considerations

- **Key Management**: Hardware Security Modules (HSM) required for signing keys.
- **Threat Vectors**: Mitigated by fail-closed architectural design and immutable blockchain anchoring.
