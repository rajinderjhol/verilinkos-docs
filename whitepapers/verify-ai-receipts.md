# How to Verify What AI Does: A Simple Guide to Cryptographic Receipts

**A Practical Introduction for Policymakers, Diplomats, and Anyone Who Needs to Trust AI Systems**

---

**Author:** Rajinder Jhol  
**Date:** 30 June 2026  
**Version:** 1.0.0

---

## Executive Summary

As Artificial Intelligence (AI) integrates into high-stakes domains like nuclear command (NC3), border security, and biological weapons monitoring, global governance faces a critical vulnerability: a dangerous reliance on blind trust. Traditional accountability mechanisms depend on unverified system logs and subjective operator testimonies, leaving international bodies unable to detect data manipulation, automated bias, or human "rubber-stamping."

To bridge this gap, this document proposes a shift to a zero-trust architecture through the deployment of automated **Cryptographic Receipts** generated at the exact moment of an AI decision.

This technical framework turns abstract accountability into a self-contained, domain-agnostic piece of mathematical evidence. A single receipt independently proves ten distinct facts—including the system's origin, input data fidelity, exact model identity, secure hardware execution, and verified human oversight (via dwell time and screen context).

Operating under 1 millisecond on standard hardware using classical cryptography, the framework remains highly practical; furthermore, even when upgraded to quantum-safe standards (NIST ML-DSA) to protect long-term state secrets, generation times are estimated to remain under 2 milliseconds on standard systems (and under 400 milliseconds on constrained field edge devices), providing a resilient toolkit for offline, future-proof deployment.

For international policymakers and diplomats, this framework delivers a powerful mechanism to operationalize disarmament treaties and protect national sovereignty. It answers the **"Grossi Imperative"** by replacing promises of good intent with objective, verifiable proof, making it possible to enforce compliance in the Biological Weapons Convention (BWC) without exposing sensitive, proprietary data.

Furthermore, it empowers importing nations—particularly in the Global South—to verify foreign-built AI systems locally and offline, ensuring robust, sovereign oversight independent of external technology vendors.

---

## Table of Contents

1. The Problem in Plain English
2. One Concrete Example: Border Security
3. What a Cryptographic Receipt Actually Is
4. The Ten Things a Cryptographic Receipt Proves
5. Why This Matters for Sovereignty
6. Nuclear Verification: The Grossi Imperative
7. Biological Weapons: Verification Without Trust
8. Chemical Weapons and Missile Systems
9. What We're Proposing: A Practical Toolkit
10. What You Can Do
11. Glossary of Key Terms
12. References and Further Reading

---

## 1. The Problem in Plain English

Imagine you're responsible for border security in your country. An AI system flags a traveler as "high risk." You have to decide whether to act on that recommendation.

Here's the uncomfortable question: **How do you know the AI got it right?** How do you verify what the AI actually did—not what it tells you it did, but what it actually did?

This is not a hypothetical. AI systems are increasingly making decisions that affect real lives. In border security, humanitarian operations, military command, healthcare, finance, and—most critically for this discussion—nuclear command and biological weapons verification, AI is being deployed without a reliable way to independently verify its actions after the fact.

### The Problem

Most AI accountability still relies on trust. The system tells you what it did. The operator tells you they reviewed it. You have to take their word for it.

### The Need

We need a way to independently verify AI decisions—**without trusting the AI or its operator**. This is the essence of a zero-trust architecture: verify everything, trust nothing.

### Why Zero-Trust Architecture Matters

A zero-trust approach assumes that no system, user, or network is inherently trustworthy. Every action must be verified independently.

| Traditional Approach | Zero-Trust Approach |
|---------------------|---------------------|
| Trust the system logs | Verify cryptographic receipts independently |
| Trust the operator's word | Verify human oversight through cryptographic proof |
| Trust the vendor's attestations | Verify model identity and execution environment |
| Trust the data hasn't been manipulated | Verify input fidelity through cryptographic hashes |

**The shift:** Moving from "I trust you because you say so" to "I can verify what happened regardless of who you are."

---

## 2. One Concrete Example: Border Security

Let's walk through a real scenario to understand the problem and the solution.

### The Scenario

An AI system at a border crossing analyzes travelers against watchlists, behavior patterns, and risk indicators. It flags a traveler as "high risk" and recommends secondary screening.

**Who is accountable for this recommendation?**
- The AI system developer?
- The border agency that deployed the system?
- The officer who accepted the AI's recommendation?
- The government that funded the system?

### The Problem Without Cryptographic Receipts

**What happens today:**
1. The AI analyzes the traveler and flags them as "high risk"
2. The border officer reviews the flag and approves the recommendation
3. The traveler is sent to secondary screening

**What cannot be independently verified:**
- What specific data did the AI actually analyze?
- What exact rules or model version made the decision?
- Did the officer truly review the evidence, or just click "approve"?
- Was the traveler's file tampered with or manipulated?
- Would an independent investigator be able to check what happened?

**The accountability gap:** If something goes wrong—the traveler is wrongly detained, evidence emerges of bias, or the system is manipulated—there is no independently verifiable evidence of what actually happened. You have to trust the system logs and the operator's testimony.

### The Solution With Cryptographic Receipts

**What happens with receipts:**

1. **Decision Moment:** The AI analyzes the traveler and generates a recommendation
2. **Receipt Creation:** A cryptographic receipt is automatically created at the exact moment of decision, capturing:
   - What data was analyzed (hash of the input)
   - What decision was made
   - Why the decision was made (context, policy rules)
   - Who (if anyone) approved it
   - How long the human reviewer spent looking at it
   - When it happened (timestamp)
3. **Human Oversight:** The border officer reviews the recommendation. The system tracks:
   - How long the officer spent reviewing (dwell time)
   - What information was displayed on screen (context hash)
   - The officer's cryptographic key binding (proving it was really them)
4. **Execution:** The traveler is sent to secondary screening
5. **Verification:** Any independent party can later verify the receipt:
   - Check the digital signature (proves the border agency issued it)
   - Verify the hashes (proves nothing was changed)
   - Validate the timestamps (proves when it happened)
   - Review the human oversight data (proves actual review occurred)

### What This Enables

| Stakeholder | What They Gain |
|-------------|----------------|
| **Border Agency** | Prove to oversight bodies that procedures were followed; audit system performance; demonstrate compliance |
| **Traveler** | Have a basis to challenge decisions with verifiable evidence; trust that decisions were reviewed |
| **Independent Investigators** | Verify what happened without trusting the agency's word; check for systemic patterns or manipulation |
| **Other Countries** | Verify compliance with agreements; trust AI-assisted decisions across borders |

---

## 3. What a Cryptographic Receipt Actually Is

### A Simple Analogy

Think of a cryptographic receipt like a restaurant bill with a few special properties:

| Traditional Receipt | Cryptographic Receipt |
|---------------------|----------------------|
| Shows items ordered | Shows what the AI analyzed |
| Shows price | Shows what decision was made |
| Shows date | Shows who approved it |
| Shows tip | Shows when it happened |
| | Shows how long they reviewed |
| | Shows what was on their screen |
| | **Cryptographic proof it can't be forged or changed** |

### The Technical "Receipt" Structure

A cryptographic receipt contains:

| Component | Plain English Explanation |
|-----------|--------------------------|
| **Input Hash** | A digital fingerprint of what the AI analyzed—like a fingerprint of the traveler's file. Change even one character, and the fingerprint changes completely. |
| **Decision Output** | What the AI actually concluded—e.g., "high risk, recommend secondary screening." |
| **Context** | Why the decision was made: the policy rules applied, the jurisdiction, the time, the system version. |
| **Evidence Hashes** | Links to supporting data (the AI's reasoning, training data references) |
| **Digital Signature** | Cryptographically proves the border agency issued it and it hasn't been altered |
| **Human Oversight** | How long the officer reviewed, what was on their screen, and a cryptographic proof they approved it |
| **Temporal Anchor** | Optional proof the receipt existed at a specific time (like a notary stamp) |

---

## 4. The Ten Things a Cryptographic Receipt Proves

A cryptographic receipt is not just a log entry. It is a self-contained piece of cryptographic evidence. When an independent party verifies a receipt, they can confirm **ten distinct facts** about the AI's decision:

| # | What It Proves | Plain English Explanation | Why It Matters for Disarmament |
|---|----------------|--------------------------|-------------------------------|
| 1 | **Origin** | Who issued the decision | Prevents impersonation; proves a specific state/system acted |
| 2 | **Integrity** | The record has not been altered | Ensures evidence isn't tampered with after the fact |
| 3 | **Timeliness** | The decision existed at a specific point in time | Proves actions weren't back-dated; critical in a "time of distrust" |
| 4 | **Input Fidelity** | Exactly what data the AI analyzed | Proves the AI wasn't fed false/malicious data |
| 5 | **Result Integrity** | Exactly what the AI decided or recommended | Proves the AI's true output, not a human's rephrasing |
| 6 | **Policy Context** | Which legal, ethical, or operational rules governed the decision | Proves BWC safety protocols or nuclear "red lines" were applied |
| 7 | **Model Identity** | Which specific version of the AI model made the decision | Prevents "model swapping" (using an unapproved, biased model) |
| 8 | **Execution Environment** | The AI ran in a secure, uncompromised hardware environment | Proves the system wasn't hacked or running malicious code |
| 9 | **Human Oversight** | A human genuinely reviewed the decision (dwell time, context hash) | Proves "human control" was maintained; prevents rubber-stamping |
| 10 | **Non-Repudiation** | The human operator cannot deny they approved it | Locks in accountability; prevents the "I wasn't responsible" excuse |

### Why These Ten Proofs Matter for Disarmament

| Disarmament Challenge | How the Ten Proofs Address It |
|-----------------------|------------------------------|
| **NC3 vulnerabilities** | Proofs #4 (Input Fidelity) and #8 (Execution Environment) directly counter undetected data manipulation in nuclear systems |
| **BWC dual-use problem** | Proof #6 (Policy Context) proves the intent and protocol applied to biological research, distinguishing legitimate vaccine development from prohibited weaponization |
| **The Grossi Principle** | Proofs #9 and #10 ensure human intent is cryptographically locked in, turning "intentions" into "verifiable evidence" |
| **Verification in distrust** | All ten proofs combine to enable independent verification without trusting the system or its operator |
| **Arms control compliance** | The full receipt provides a complete, independently checkable audit trail of AI-assisted actions |

---

## 5. Why This Matters for Sovereignty

### The Problem Many Countries Face

Most countries do not develop their own foundational AI models. They import them from other countries—primarily the United States, China, and Europe.

**The challenge:** When you import an AI system, you're importing its logic, its biases, and its blind spots. How do you verify what it actually does, independent of the vendor?

### Cryptographic Receipts Provide Sovereignty

| Without Receipts | With Receipts |
|------------------|---------------|
| You depend on the vendor's logs | You generate your own verifiable evidence |
| You can't audit independently | Anyone can verify the receipt |
| You have to trust the foreign system | You can verify locally, offline |
| You can't build local capability | You build local verification infrastructure |

**The key insight:** You don't need to develop your own AI to have sovereignty over its verification. You can verify imported systems locally without:
- Sending your data abroad
- Depending on the vendor
- Building massive compute infrastructure
- Being online

### Zero-Trust Sovereignty

This approach embodies a zero-trust architecture applied to sovereignty: **you don't need to trust the vendor, the cloud provider, or the network.** You can verify every decision independently, using only the cryptographic receipt and publicly available verification keys.

This is **sovereignty through cryptography**, not through data localization or vendor agreements.

---

## 6. Nuclear Verification: The Grossi Imperative

> *"Intentions are not enough. We need a very strong verification system."*
>
> — **Director General Rafael Grossi**, International Atomic Energy Agency (IAEA), AISE26 Fireside Chat, June 2026

### The Nuclear-AI Challenge

AI systems are increasingly integrated into nuclear command, control, and communications (NC3) systems. This creates new vulnerabilities:
- AI recommendations could be manipulated
- Human operators might rubber-stamp AI suggestions
- Accountability could be diffused across systems and people
- Detection of interference with nuclear infrastructure requires independently verifiable evidence

### How Cryptographic Receipts Address Nuclear Threats

| Threat | How Receipts Help |
|--------|-------------------|
| Undetected AI manipulation | Receipts prove what data the AI actually analyzed and what it recommended |
| Human rubber-stamping | Dwell time proves actual review; context hash proves what they saw |
| Disputed accountability | Cryptographic signatures prove who approved what, when |
| "Trust me" governance | Independent verification replaces trust with evidence |
| Infrastructure interference | Receipts provide tamper-evident audit trails for all AI-related actions |
| NC3 system compromise | Cryptographic attestation ensures detection of unauthorized modifications |

### Why This Matters for Nuclear Disarmament

In a "time of distrust," states need verification mechanisms that don't require trust:
- **Compliance verification:** States can prove they followed agreed procedures
- **Confidence-building:** Independent verification builds trust through evidence—not through promises
- **Transparency:** States can demonstrate adherence to "red lines" (e.g., not delegating launch authorization to AI)
- **Arms control verification:** Cryptographic receipts provide a mechanism for verifying compliance with agreed rules in AI-enabled defense systems

---

## 7. Biological Weapons: Verification Without Trust

> *"The BWC essentially functions as a gentleman's agreement with diplomatic trappings. States report compliance, but there is no independent verification mechanism."*
>
> — **UNIDIR Analysis** on Biological Weapons Verification

### The Verification Gap

The Biological Weapons Convention (BWC) faces a fundamental challenge: **it has no verification mechanism.** States report compliance, but there is no independent way to verify what actually happened.

### The Dual-Use Problem

Biological research is inherently dual-use. The same equipment and techniques used to develop vaccines can also be used to weaponize pathogens. This makes verification extraordinarily difficult.

### How Cryptographic Receipts Address Biological Verification

| Challenge | How Receipts Address It |
|-----------|-------------------------|
| Dual-use ambiguity | Receipts capture the context and human oversight behind AI-assisted decisions—proving what was done and why |
| No verification mechanism | Receipts provide independently verifiable evidence that can be checked by any party |
| National security concerns | Verification doesn't require sharing sensitive data—only the receipt needs to be shared |
| Export control enforcement | Receipts can authenticate licensing documentation and prove chain of custody |
| Capacity constraints | Offline, low-compute verification is accessible to all states |
| Sovereignty concerns | States verify locally without surrendering data sovereignty |

### Current Policy Window

In September 2025, President Trump announced at the United Nations that the United States would **"lead an international effort to enforce the Biological Weapons Convention by pioneering an AI verification system that everyone can trust."** Russia called the proposal "brilliant."

**The opportunity:** This creates a genuine policy window—not for AI to be a "panacea," but for concrete verification tools to enter the conversation. Cryptographic receipts provide a technically grounded proposal that can inform this discussion.

---

## 8. Chemical Weapons and Missile Systems

While this guide focuses on nuclear and biological verification due to their immediate policy relevance, the cryptographic receipt framework is equally applicable to other WMD domains:

### Chemical Weapons Verification

| Challenge | How Receipts Help |
|-----------|-------------------|
| Dual-use chemical precursors | Receipts capture what was ordered, processed, and why—proving intent |
| Verification of destruction | Cryptographic records prove stockpile destruction procedures were followed |
| Export control enforcement | Chain-of-custody receipts authenticate licensing documentation |
| OPCW compliance | Independent verification of chemical facility activities |

### Missile and Drone Systems

| Challenge | How Receipts Help |
|-----------|-------------------|
| Autonomous weapons verification | Receipts prove what AI systems recommended and what humans approved |
| Missile test verification | Cryptographic records of launch parameters and decision-making |
| Arms control compliance | Verifiable evidence that missile systems operate within agreed parameters |
| Dual-use tracking | Chain of custody for missile-related components |

### Domain-Agnostic Design

The framework is designed to be **domain-agnostic**. Whether the domain is nuclear, biological, chemical, or missile systems, the same cryptographic receipt structure provides verifiable accountability.

---

## 9. What We're Proposing: A Practical Toolkit

### A Reference Implementation, Not Just Theory

This framework has been implemented in **VeriLinkOS v2.4.0**, a comprehensive active governance platform for autonomous AI systems. This implementation demonstrates the technical feasibility of the concepts and provides a reference model for further development and standardization.

### What It Does

| Capability | Plain English Explanation |
|------------|--------------------------|
| **Generates Cryptographic Receipts** | Creates tamper-evident records for every AI decision |
| **Enforces Human Oversight** | Ensures humans review decisions and proves they did |
| **Works Offline** | Verifies receipts without internet—essential for field operations |
| **Supports Any AI System** | Works with existing AI infrastructure |
| **Enables Sovereign Verification** | Countries can verify receipts locally without vendor dependence |
| **Prevents Unauthorized Actions** | Blocks AI actions that don't meet rules—before they happen |

### Who It's For

| Stakeholder | What They Get |
|-------------|---------------|
| **States and Regulators** | Enforceable compliance verification |
| **International Organizations** | Cross-border interoperability and trust |
| **Humanitarian Agencies** | Verify AI decisions without sharing sensitive data |
| **Global South Countries** | Sovereignty-preserving verification with low compute needs |
| **Industry** | Auditable AI systems that build trust |
| **Nuclear Verification Regimes** | Independently verifiable evidence for compliance |
| **BWC State Parties** | A practical verification mechanism |

### Performance Characteristics

| Metric | What It Means |
|--------|---------------|
| **Receipt Generation** | Under 1 millisecond—doesn't slow down AI systems |
| **Verification** | Under 0.1 millisecond per receipt—practically instant |
| **Offline Verification** | Works without internet—essential for field operations |
| **Storage** | ~1-2 KB per receipt—tiny, even for millions of decisions |

### Implementation Status

| Capability | Status |
|------------|--------|
| Receipt Generation | ✅ Demonstrated |
| Verification | ✅ Demonstrated |
| Anchoring Mechanisms | ✅ Multiple options |
| Human Oversight | ✅ Demonstrated |
| Governance Dashboard | ✅ Demonstrated |
| Offline Verification | 🔄 Under Development |
| IETF SCITT Integration | 🔄 Architecture-Ready |
| Nuclear Verification Pilots | 🔄 Under Discussion |
| Biological Verification Pilots | 🔄 Under Discussion |

### Alignment with International Standards

| Framework | Alignment |
|-----------|-----------|
| **EU AI Act** (Arts. 12, 14, 50) | Tamper-evident logging, human oversight, transparency |
| **ISO/IEC 42001** | Auditability and AI management system traceability |
| **GDPR** (Art. 17) | Privacy-preserving data handling |
| **IETF SCITT** | Supply chain integrity, transparency, and trust |
| **Singapore AI Verify** | Governance transparency dimensions |
| **CCW/GGE Discussions** | Autonomous weapons verification |

---

## 10. What You Can Do

### 1. Evaluate the Toolkit

The reference implementation (VeriLinkOS v2.4.0) is available for immediate technical evaluation upon request. You can:
- Test it with your own scenarios
- Verify the technical claims
- Assess its suitability for your context
- Provide feedback for refinement

### 2. Pilot It in Your Context

We're looking for pilot partners to test this in real settings:

| Domain | Example Scenario |
|--------|------------------|
| Nuclear Verification | Verifying compliance in nuclear disarmament contexts |
| Biological Weapons | BWC compliance verification and high-containment lab governance |
| Chemical Weapons | OPCW verification and chemical facility monitoring |
| Missile Systems | Arms control verification for missile systems |
| Border Security | A West African border crossing pilot |
| Humanitarian Operations | Refugee camp logistics verification |
| Healthcare | Clinical decision support audit |
| Finance | Anti-money laundering compliance |

### 3. Shape the Standards

This is a reference architecture, not a concluded framework. We welcome:
- Your feedback
- Your adaptations
- Your contributions to standardization
- Your participation in the governance discussions

### 4. Engage With UNIDIR's Centre of Excellence

The UNIDIR Centre of Excellence on AI, Peace and Security—launched June 2026—provides the perfect platform to:
- Discuss verification standards
- Develop pilot programs
- Build capacity in Global South countries
- Shape the future of verifiable AI governance

### 5. Contribute to International Processes

| Process | How Your Engagement Matters |
|---------|----------------------------|
| BWC Working Group | Contribute to developing verification proposals |
| CCW/GGE | Inform autonomous weapons verification discussions |
| IETF SCITT | Contribute to emerging standards |
| UNIDIR Centre of Excellence | Support the "Action" pillar with concrete tools |
| Switzerland's 2027 AI Summit | Present practical governance solutions |

---

## 11. Glossary of Key Terms

| Term | Plain English Definition |
|------|--------------------------|
| **Anchoring** | Linking a receipt to an external record (like a ledger or blockchain) to prove it existed at a specific time. |
| **Biological Weapons Convention (BWC)** | An international treaty prohibiting biological weapons. Unlike the nuclear non-proliferation regime, it has no verification mechanism. |
| **Context Attestation** | A cryptographic proof that the human reviewer saw exactly what the system displayed at the time of approval—not a manipulated or misleading view. |
| **Constitutional AI** | Guardrails that enforce ethical and legal rules before AI actions are executed. |
| **Cryptographic Receipt** | A tamper-evident record of what an AI did, when, why, and who approved it—verifiable by anyone, anywhere, without needing the original system. |
| **Digital Signature (Ed25519)** | A digital fingerprint that proves a specific entity approved something—like a unique, unforgeable digital signature. |
| **Dual-Use** | Technology that can serve both legitimate and prohibited purposes—a central challenge in biological and chemical weapons verification. |
| **Dwell Time** | How long a human operator spent reviewing an AI decision before approving it—helps prove real attention, not rubber-stamping. |
| **Hash / Hash Function** | A mathematical operation that creates a unique "fingerprint" of any data. Change the data even slightly, and the hash changes completely. |
| **Human-in-the-Loop (HITL)** | A requirement that a human review and approve AI decisions before they're executed. |
| **Key Binding** | A cryptographic link between the human reviewer's signature and a physical key or credential—proving it was really them, not someone impersonating them. |
| **Merkle Tree** | A way to pack many receipts together so verifying one proves all are valid—efficient for large-scale systems. |
| **Nuclear Command, Control, and Communications (NC3)** | The systems and processes used for nuclear decision-making—increasingly integrated with AI. |
| **Offline Verification** | Verifying receipts without internet—essential for field operations and connectivity-limited contexts. |
| **Organisation for the Prohibition of Chemical Weapons (OPCW)** | The international body responsible for implementing the Chemical Weapons Convention. |
| **Rubber-Stamping** | When a human approves an AI recommendation without genuine review—detected through dwell time and context verification. |
| **Sovereign AI** | AI systems that a state can audit and contextualize locally, without vendor dependence. |
| **Tamper-Evident** | A property that makes it detectable if something has been altered—even if you can't prevent alteration, you can detect it. |
| **Temporal Anchor** | A proof that a receipt existed at a specific time—like a notary stamp with a date. |
| **Trusted Execution Environment (TEE)** | A secure area of a processor that guarantees code and data are protected—providing hardware-level proof of execution integrity. |
| **Verification** | The ability for any independent party to check that a receipt is authentic and unaltered. |
| **Verification Gap** | The growing mismatch between what AI systems can do and our ability to independently verify what they've done. |
| **Zero-Trust Architecture** | A security model that doesn't trust any system, user, or network by default—everything must be independently verified. |

---

## 12. References and Further Reading

### Primary Sources

Jhol, R., "Cryptographic Accountability for High-Risk AI Systems: A Reference Architecture for Verifiable AI Governance, Human Oversight, and Algorithmic Accountability," Conference Contribution to the Global Conference on AI, Security and Ethics (AISE26), United Nations Institute for Disarmament Research (UNIDIR), June 2026.

Jhol, R., "Cryptographic Accountability for High-Risk AI Systems: A Practical Reference Architecture for Verifiable AI Governance, Human Oversight, and Algorithmic Accountability," Post-Conference Contribution to AISE26, UNIDIR Centre of Excellence on AI, Peace and Security, June 2026.

VeriLinkOS v2.4.0 Reference Implementation, 2026. Available for technical evaluation upon request.

### Technical References

Avni, S., et al., "Verification via Proof-Carrying Output," Cryptology ePrint Archive, 2026.

Le Ray, R., "Policy-Governed RAG," 2025.

Wang, H., "HJS: An Accountability Layer for AI Agents," IETF Draft, 2026.

Epistria, "Provable Digital Lifecycles," SEC Task Force Submission, 2025.

Haberkamp, M., "Intent Provenance Protocol (IPP)," IETF Draft, 2026.

### UNIDIR Documents

UNIDIR, "Global Conference on AI, Security and Ethics 2026: Conference Agenda and Proceedings," UNOG, June 2026.

UNIDIR, "Centre of Excellence on AI, Peace and Security," Launch Announcement, June 2026.

UNIDIR, "UNIDIR Strategy 2026-2030."

UNIDIR, "Report of the Director on the Activities of the Institute for 2023," UN Doc. A/79/...

UNIDIR, "UNIDIR Roundup and Outlook 2025."

UNIDIR, "Science and Technology Watchtower: Emerging Technologies and the Biological Weapons Convention," 2025-2026.

UNIDIR, "Nuclear Verification Field Exercises: On-Site Inspections and Satellite Surveillance," 2025.

### Keynote Speeches

Grossi, R., Remarks at AISE26 Fireside Chat, United Nations Office at Geneva, June 2026.

Grossi, R., Press Conference Statements on Nuclear Verification, June 2026.

Geiss, R., Opening Remarks at AISE26, United Nations Office at Geneva, June 2026.

Nakamitsu, I., Remarks at AISE26 Fireside Chat, United Nations Office at Geneva, June 2026.

### Standards and Frameworks

European Union, "EU AI Act," Regulation (EU) 2024/1689, Arts. 12, 14, 50.

International Organization for Standardization, "ISO/IEC 42001: Artificial Intelligence - Management System," 2023.

European Union, "General Data Protection Regulation (GDPR)," Regulation (EU) 2016/679, Art. 17.

IETF, "Supply Chain Integrity, Transparency, and Trust (SCITT)," Working Group Documents.

Government of Singapore, "AI Verify Foundation: Testing Framework and Governance Guidelines," Version 1.5.

### Conference Contributions Cited

Ogada, B., "Sovereignty Under Code: AI Infrastructure Dependence and the Reconfiguration of Security Decision-Making in Africa," Lightning Talk, AISE26, June 2026.

Hassan, J.M., "The Role of National Strategies for the Governance of AI in the Military Domain: Best Practices from Kenya," Panel Presentation, AISE26, June 2026.

AwakEssien, M., "Closing the Governance Gap: African Perspectives on Inclusive AI Frameworks for International Peace and Security," Panel Presentation, AISE26, June 2026.

Robinson, A., "Collateral Code: How Military AI Governance Failures Disproportionately Threaten Small Island Developing States," Lightning Talk, AISE26, June 2026.

Onoja, C. and Alaita, F., "Human-Centered Proactivity in Defence AI: Rethinking Agency, Judgment and Responsibility," Thematic Deep Dive, AISE26, June 2026.

Tahseen, H., "When AI Cannot Distinguish Distress from Threat: The Case for Behavioural Validity in AI Governance," Presentation, AISE26, June 2026.

Pinelis, J. and Vignard, K., "Lost in Translation Series: Bias," Deep Dive, AISE26, June 2026.

Alagamy, M., "The Clock Is Already Running: How Quantum Breaks the Security Promises Underneath AI Governance," Lightning Talk, AISE26, June 2026.

Kaluderovic, C., "Minds on the Front Line: Governing AI Tools for Military and Veteran Mental Health," Presentation, AISE26, June 2026.

Kennedy, M. and Heath, N., "'This Black Darkness of Not Knowing': Can Prisoner-of-War AI Deepfakes Be Governed by IHL?" Deep Dive, AISE26, June 2026.

### Additional Resources

Stockholm International Peace Research Institute (SIPRI), "AI and DLT for High-Containment Laboratory Governance," 2025-2026.

Geneva Centre for Security Policy (GCSP), "International Security and Military Implications of Agentic AI," 2026.

United Nations General Assembly, "United Nations Institute for Disarmament Research Statute," Article II and Article III.

United Nations, "Biological Weapons Convention," 1972 (and subsequent Review Conference outcomes).

United Nations, "Chemical Weapons Convention," 1993 (and subsequent Review Conference outcomes).

International Atomic Energy Agency (IAEA), "Safeguards and Verification," Ongoing Publications.

Organisation for the Prohibition of Chemical Weapons (OPCW), "Verification," Ongoing Publications.

---

## Contact

**Rajinder Jhol**  
Academy for AI Diplomacy and International Affairs (Designate)  
📧 **Email:** rajinderjhol@gmail.com  
🔗 **LinkedIn:** https://www.linkedin.com/in/rjhol/

---

## About This Document

This guide accompanies the full technical papers:
- *"Cryptographic Accountability for High-Risk AI Systems: A Reference Architecture for Verifiable AI Governance, Human Oversight, and Algorithmic Accountability"* (AISE26 Pre-Conference Contribution)
- *"Cryptographic Accountability for High-Risk AI Systems: A Practical Reference Architecture for Verifiable AI Governance, Human Oversight, and Algorithmic Accountability"* (AISE26 Post-Conference Contribution, UNIDIR Centre of Excellence)

Offered as a neutral, evidence-based contribution to the multistakeholder process on verifiable AI governance.

---

**Copyright © 2026 Rajinder Jhol**

This work is licensed under a Creative Commons Attribution - Non Commercial - No Derivatives 4.0 International License.

**Disclaimer:** The findings, interpretations, and conclusions expressed herein are those of the author and do not necessarily reflect the views of the United Nations, UNIDIR, or its Member States. This is a non-normative technical contribution intended to support ongoing multistakeholder dialogue. It does not represent the position of any organization or government and is offered as a neutral, evidence-based contribution to the public record.

