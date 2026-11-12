# ARAS v1.0 — The Agentic Risk Assessment Standard

**Status:** Normative Standard (Draft for Public Review)
**Date:** 11 November 2026
**Authority:** Open standard, published for adoption by insurers, MGAs, enterprises, and standards bodies.
**Companion Specifications:** VAP Receipt v3.0 (evidence format), WEF ACAP (governance framework), OWASP Securing Agentic Applications v2 (security controls).

---

## 0. Preamble

This standard defines how to measure, score, and classify the operational risk of autonomous AI agents for the purpose of underwriting liability insurance. It establishes a common covariate set, a scoring methodology, a risk tier classification, and evidence requirements.

The standard is designed to solve a specific problem: **insurers cannot underwrite a risk nobody has quantified.** Today, agentic risk is treated as uninsurable because there is no standardized way to measure agent behavior, evidence quality, or authority scope. ARAS provides that measurement.

ARAS is a **measurement standard, not a pricing standard.** It does not prescribe premiums. It prescribes the inputs an underwriter uses to derive premiums. This distinction matters: the standard is neutral across carriers, jurisdictions, and lines of business.

---

## 1. Scope

### 1.1 In scope

- The covariate set required for agentic risk assessment.
- The scoring methodology to compute a composite risk score from those covariates.
- The five-tier risk classification and the evidence/control requirements for each tier.
- The standardized reporting format ("Agentic Loss Run") that insureds submit to insurers.
- Conformance requirements for implementations claiming ARAS compliance.

### 1.2 Out of scope

- Premium calculation, pricing models, or capital allocation (underwriter responsibility).
- Regulatory compliance attestation (jurisdiction-specific; handled by other standards).
- Security controls (defined in OWASP Securing Agentic Applications v2).
- Governance frameworks (defined in WEF ACAP).
- Technical receipt formats (defined in VAP Receipt v3.0).

### 1.3 Intended audience

- Underwriters and actuaries pricing agentic liability.
- MGAs and insurers writing affirmative AI liability policies.
- Enterprises procuring coverage for autonomous agents.
- Regulators assessing market risk in agentic commerce.
- Auditors verifying agent governance and evidence.

---

## 2. Normative References

The following documents are indispensable for the application of this standard:

1. **VAP Receipt v3.0** — Verifiable Action Protocol receipt format (canonical JSON, dual-signature, offline verification).
2. **WEF AI Agents in Action: A Playbook for Trusted Adoption, Authorization and Scaling (May 2026)** — Agent Capability and Authorization Profile (ACAP).
3. **OWASP Securing Agentic Applications v2 (2026)** — Execution integrity, runtime enforcement, and hooks.
4. **ISO/IEC 42001:2023** — AI management systems.
5. **NIST AI Risk Management Framework (AI RMF 1.0)** — AI risk taxonomy.
6. **EU AI Act (Regulation 2024/1689)** — Article 14 (human oversight), Article 50 (transparency), Article 71 (post-market monitoring).
7. **FIDO Alliance Agentic Authentication Working Group specifications** (in development) — human authorization standards.

Where this standard references a covariate defined in one of the above, the definition in the referenced document is authoritative.

---

## 3. Terms and Definitions

**3.1 Agent.** An autonomous software system that interprets context and executes actions on behalf of a human or organization within delegated authority.

**3.2 Mandate.** The cryptographically signed authorization specifying what an agent may do, under what constraints, and for how long. The mandate is the human's grant of authority.

**3.3 Execution Gate.** The runtime enforcement point that checks an agent's proposed action against the mandate before the action executes. A conforming gate is fail-closed.

**3.4 Receipt.** The cryptographically signed record of an agent action, binding the human's authorization, the agent's proposed action, the gate's decision, and the execution outcome. Defined in VAP Receipt v3.0.

**3.5 Evidence Quality.** The degree to which a receipt provides independently verifiable proof of authorization and enforcement, as opposed to operator-attested assertions.

**3.6 Data Quality Index (DQI).** A composite score (0.0–1.0) measuring the completeness, coherence, and adequacy of the evidence supporting an agent action. Component definitions in §5.2.

**3.7 Block Rate.** The fraction of attempted actions blocked by the execution gate over a defined measurement period. A behavioral indicator of constraint pressure.

**3.8 Human Override Rate.** The fraction of agent proposals rejected or modified by human reviewers in the HITL queue. A behavioral indicator of proposal quality.

**3.9 Pre-Mandate Commitment.** A hash commitment made by the human to their intent *before* the agent proposes an action. Detects adverse selection and post-hoc rationalization.

**3.10 Delegation Depth.** The number of hops in a delegation chain from the human principal to the executing agent. Depth 1 = direct authorization; depth >1 = multi-hop delegation.

**3.11 Lethal Trifecta Exposure.** Whether an agent holds all three of: (a) access to sensitive data, (b) exposure to untrusted input, (c) ability to communicate externally. Per OWASP §2.7.3.

**3.12 Coverage.** The fraction of a defined population of agent actions for which a conforming receipt exists. Coverage below 100% indicates governance gaps.

---

## 4. The Risk Model

### 4.1 Conceptual framework

Agentic risk is not a property of the model. It is a property of the **operating envelope**: the combination of authority granted, evidence produced, behavior observed, and environment deployed. ARAS models risk as a function of four orthogonal dimensions:

```
Risk Score = f(Authority Risk, Evidence Risk, Behavioral Risk, Environment Risk)
```

Each dimension is computed from observable covariates (defined in §5). The dimensions are weighted and combined into a composite score (defined in §6). The score maps to a risk tier (defined in §7).

### 4.2 Why four dimensions

The four dimensions correspond to the four questions an underwriter asks:

- **Authority Risk:** How much damage could this agent cause if it acted outside its mandate?
- **Evidence Risk:** If it did act outside its mandate, could we prove it?
- **Behavioral Risk:** How likely is it to act outside its mandate?
- **Environment Risk:** What is the operational context that shapes that likelihood?

No single dimension is sufficient. An agent with tight authority but no evidence is a different risk from an agent with broad authority and excellent evidence. The four-dimension model captures both.

### 4.3 Calibration principle

The weights and thresholds in this standard are **initial estimates based on expert judgment**, calibrated against the current state of agentic deployments. They are expected to be refined as loss experience accumulates. ARAS v1.0 establishes the measurement framework; ARAS v2.0 will incorporate actuarial calibration.

---

## 5. The Covariate Set

This section defines the covariates that must be captured for each agent under assessment. Covariates are grouped by dimension. For each covariate, the standard defines the field, the source (receipt field or aggregate), and the measurement period.

### 5.1 Authority Risk covariates

| ID | Covariate | Source | Measurement Period |
|---|---|---|---|
| A1 | Maximum spend limit in mandate | Receipt `mandate.scope.spend_limit` | Per mandate |
| A2 | Merchant/counterparty allowlist breadth | Receipt `mandate.scope.allowlist` | Per mandate |
| A3 | Action category breadth | Receipt `mandate.scope.categories` | Per mandate |
| A4 | Mandate duration (expiry − issuance) | Receipt `mandate.scope.expiry` | Per mandate |
| A5 | Delegation depth | Receipt `multi_hop_chain` length | Per action |
| A6 | Revocation capability | System config (boolean) | Per deployment |
| A7 | HITL threshold (action tier requiring human approval) | System config | Per deployment |
| A8 | Lethal Trifecta exposure (boolean) | System config (per OWASP §2.7.3) | Per deployment |

### 5.2 Evidence Risk covariates

| ID | Covariate | Source | Measurement Period |
|---|---|---|---|
| E1 | DQI composite score | Receipt `commerce_dqi.score` | Per action |
| E2 | DQI component scores (7) | Receipt `commerce_dqi.components` | Per action |
| E3 | Receipt completeness (% required fields present) | Receipt validation | Per action |
| E4 | Signature validity (dual vs single) | Receipt `proof` | Per action |
| E5 | Offline verification result | Verifier output | Per action |
| E6 | Pre-mandate commitment match | Receipt `pre_mandate_commitment.match` | Per action |
| E7 | Evidence provenance completeness | Receipt `evidence_provenance` | Per action |
| E8 | Coverage (fraction of actions with conforming receipts) | Aggregate | Per measurement period |

### 5.3 Behavioral Risk covariates

| ID | Covariate | Source | Measurement Period |
|---|---|---|---|
| B1 | Block rate | Aggregate (blocked / total) | 30-day rolling |
| B2 | Human override rate | Aggregate (overrides / HITL proposals) | 30-day rolling |
| B3 | HITL latency (median, p95) | Aggregate (proposal → decision) | 30-day rolling |
| B4 | Escalation rate (HITL / total actions) | Aggregate | 30-day rolling |
| B5 | Novelty score distribution (mean, p95) | Receipt `novelty_score` | 30-day rolling |
| B6 | Revocation rate | Aggregate (mandates revoked mid-flight) | 30-day rolling |
| B7 | Incident count (severity-weighted) | Incident log | 12-month rolling |
| B8 | Reasoning drift signals | Monitoring output (per OWASP §3.3.1) | 30-day rolling |

### 5.4 Environment Risk covariates

| ID | Covariate | Source | Measurement Period |
|---|---|---|---|
| N1 | Deployment context tier (L1/L2/L3 per WEF ACAP §1.5) | System config | Per deployment |
| N2 | Jurisdiction | Receipt `context.jurisdiction` | Per action |
| N3 | Data classification (public/internal/confidential/restricted) | System config | Per deployment |
| N4 | Tool capability class (KC6.1–KC6.6 per OWASP) | System config | Per deployment |
| N5 | Regulatory regime (EU AI Act high-risk / limited / minimal) | System config | Per deployment |

### 5.5 Required vs. optional covariates

All covariates in §5.1–§5.4 are **required** for ARAS conformance. Implementations that cannot capture a covariate must report it as `null` and apply the default penalty defined in §6.4. A null covariate is treated as worst-case for scoring purposes (fail-closed principle).

---

## 6. Scoring Methodology

### 6.1 Overview

The composite risk score is computed in three steps:

1. **Normalize** each covariate to a 0.0–1.0 scale where 1.0 represents maximum risk.
2. **Aggregate** normalized covariates into four dimension scores using weighted means.
3. **Combine** dimension scores into a composite score using dimension weights.

### 6.2 Covariate normalization

Each covariate is normalized to [0, 1] where higher = riskier. The normalization function depends on the covariate type:

- **Bounded numeric (e.g., spend limit):** `score = min(value / reference_max, 1.0)` where `reference_max` is defined in Appendix A.
- **Fractional (e.g., block rate):** `score = value` (already in [0, 1]).
- **Boolean (e.g., Lethal Trifecta exposure):** `score = 1.0` if true, `0.0` if false.
- **Categorical (e.g., deployment tier):** mapped per Appendix A.
- **Distribution (e.g., HITL latency):** `score = 1.0 - (p95_latency / reference_max)`.

Reference maxima and categorical mappings are defined in Appendix A.

### 6.3 Dimension aggregation

Each dimension score is the weighted mean of its normalized covariates:

```
Authority_Risk  = Σ w_i × A_i   (weights in Appendix A, Table A.1)
Evidence_Risk   = Σ w_i × E_i   (weights in Table A.2)
Behavioral_Risk = Σ w_i × B_i   (weights in Table A.3)
Environment_Risk = Σ w_i × N_i  (weights in Table A.4)
```

Weights within each dimension sum to 1.0.

### 6.4 Null covariate penalty

If a covariate is `null` (not captured), it is assigned its worst-case normalized value (1.0) and flagged in the report. This enforces the fail-closed principle: missing evidence is treated as evidence of risk.

### 6.5 Dimension weights

The composite score is a weighted sum of the four dimension scores:

```
Composite_Score = (w_A × Authority_Risk) 
                + (w_E × Evidence_Risk)
                + (w_B × Behavioral_Risk)
                + (w_N × Environment_Risk)
```

Initial weights (v1.0):

| Dimension | Weight | Rationale |
|---|---|---|
| Authority Risk | 0.30 | The primary driver of loss severity |
| Evidence Risk | 0.30 | The primary driver of liability attribution |
| Behavioral Risk | 0.25 | The primary driver of loss frequency |
| Environment Risk | 0.15 | Modifies the other three but is less directly causal |

These weights are initial estimates. ARAS v2.0 will recalibrate them against loss experience.

### 6.6 Score interpretation

The composite score ranges from 0.0 (minimal risk) to 1.0 (maximum risk). Interpretation bands:

| Composite Score | Interpretation |
|---|---|
| 0.00–0.20 | Excellent: tight authority, strong evidence, stable behavior |
| 0.20–0.40 | Good: minor gaps, manageable risk |
| 0.40–0.60 | Moderate: notable gaps requiring attention |
| 0.60–0.80 | Elevated: significant gaps, enhanced review required |
| 0.80–1.00 | Critical: unacceptable risk posture without remediation |

---

## 7. Risk Tier Classification

### 7.1 Tier definitions

The composite score maps to one of five risk tiers. Each tier carries mandatory evidence requirements, control requirements, and maximum authority grants.

| Tier | Score Range | Label | Evidence Requirement | Control Requirement | Max Authority |
|---|---|---|---|---|---|
| **1** | 0.00–0.20 | Minimal | VAP receipts (dual-signed, offline-verifiable) | Execution gate + HITL for Tier 3+ actions | Standard limits |
| **2** | 0.20–0.40 | Low | Tier 1 + pre-mandate commitment | Tier 1 + quarterly review | Standard limits |
| **3** | 0.40–0.60 | Moderate | Tier 2 + DQI ≥ 0.70 + coverage ≥ 95% | Tier 2 + mandatory HITL for Tier 2+ actions | Enhanced limits |
| **4** | 0.60–0.80 | Elevated | Tier 3 + DQI ≥ 0.85 + coverage = 100% + external audit | Tier 3 + board-level sign-off | Restricted limits |
| **5** | 0.80–1.00 | Critical | Uninsurable without remediation | Uninsurable without remediation | No authority |

### 7.2 Tier escalation triggers

An agent's tier is escalated one level if any of the following is true:

- Any incident with severity ≥ "high" in the last 12 months.
- Lethal Trifecta exposure = true and deployment tier = L3.
- Novelty score p95 > 0.7 (agent operating significantly outside training distribution).
- Coverage < 90% (governance gaps).

### 7.3 Tier de-escalation

An agent's tier is de-escalated one level only after 90 consecutive days with:
- No incidents.
- Coverage = 100%.
- DQI ≥ tier requirement + 0.05.
- Independent verification passing on a random sample of 100 receipts.

### 7.4 Underwriting implications (informative)

This section is informative; it does not prescribe pricing.

- **Tier 1–2:** Standard rates. Coverage available for all action types.
- **Tier 3:** Enhanced rates. Coverage available with HITL requirements.
- **Tier 4:** High rates. Coverage available with board sign-off and external audit.
- **Tier 5:** Coverage declined unless remediation plan executed.

---

## 8. Evidence Requirements

### 8.1 Receipt requirements by tier

Every agent action must produce a receipt meeting VAP Receipt v3.0. For ARAS conformance, the receipt must additionally contain:

| Field | Tier 1 | Tier 2 | Tier 3 | Tier 4 |
|---|---|---|---|---|
| `mandate` block with human signature | Required | Required | Required | Required |
| `dual_signature` proof (human + operator) | Required | Required | Required | Required |
| `pre_mandate_commitment` | — | Required | Required | Required |
| `commerce_dqi` block | — | — | Required | Required |
| `constraints_evaluated` (non-empty) | Required | Required | Required | Required |
| `offline_verification` result | Recommended | Required | Required | Required |
| `evidence_provenance` completeness | — | — | Required | Required |

### 8.2 Coverage requirements

Coverage is the fraction of agent actions for which a conforming receipt exists. Coverage requirements:

| Tier | Minimum Coverage |
|---|---|
| Tier 1 | 90% |
| Tier 2 | 95% |
| Tier 3 | 99% |
| Tier 4 | 100% |

Coverage below 90% automatically escalates to Tier 4 (see §7.2).

### 8.3 Audit requirements

- **Tier 1–2:** Internal audit annually.
- **Tier 3:** External audit annually.
- **Tier 4:** External audit semi-annually, with audit report filed with the insurer.

Audit scope: verification that receipts are being produced, covariates are being captured, and scores are being computed per this standard. Audit methodology is not prescribed by ARAS; it is the auditor's responsibility.

---

## 9. The Reporting Format: The Agentic Loss Run

### 9.1 Purpose

The Agentic Loss Run is the standardized report an insured submits to an insurer. It is the equivalent of the loss run in traditional liability insurance: a structured dataset of activity and incidents that the underwriter uses to price the policy and adjust claims.

### 9.2 Structure

The Agentic Loss Run is a JSON document with four sections:

**Section A: Agent Inventory**
For each agent under assessment:
- Agent identity (DID, name, version)
- Deployment context (tier, jurisdiction, data classification)
- Mandate summary (scope, duration, delegation depth)
- Current ARAS score and tier
- Coverage percentage

**Section B: Aggregate Metrics**
For each agent, over the reporting period (default: 90 days):
- Total actions
- Block rate
- Human override rate
- HITL latency (median, p95)
- Novelty score distribution
- DQI distribution (mean, p10, p90)
- Revocation count

**Section C: Incident Log**
For each incident in the reporting period:
- Incident ID
- Timestamp
- Agent identity
- Incident type (per OWASP T1–T15 taxonomy)
- Severity (low/medium/high/critical)
- Root cause (if determined)
- Remediation action
- Receipt reference (the VAP receipt ID)

**Section D: Evidence Sample**
A random sample of 100 receipts (or all receipts if fewer than 100) for independent verification. The insurer or its auditor can run the offline verifier against the sample to confirm the metrics in Section B.

### 9.3 Reporting cadence

| Tier | Reporting Cadence |
|---|---|
| Tier 1 | Annual |
| Tier 2 | Semi-annual |
| Tier 3 | Quarterly |
| Tier 4 | Monthly |

### 9.4 Format

The Agentic Loss Run MUST be a JSON document conforming to the schema in Appendix E. Human-readable PDF summaries MAY be provided in addition but do not substitute for the JSON document.

---

## 10. Conformance Requirements

An implementation claims ARAS conformance if and only if it satisfies all of the following:

### 10.1 Covariate capture

**10.1.1** The implementation MUST capture all covariates listed in §5.

**10.1.2** Covariates MUST be sourced from conforming VAP Receipt v3.0 records or from system configuration that is itself auditable.

**10.1.3** Null covariates MUST be reported as null and scored per §6.4.

### 10.2 Scoring

**10.2.1** The implementation MUST compute the composite score using the formula in §6.

**10.2.2** The implementation MUST use the normalization functions and weights defined in Appendix A.

**10.2.3** The implementation MUST produce a score that matches the reference implementation's output within ±0.01 for any conforming input.

### 10.3 Tier classification

**10.3.1** The implementation MUST map the composite score to a tier per §7.1.

**10.3.2** The implementation MUST apply the escalation and de-escalation rules in §7.2 and §7.3.

### 10.4 Evidence

**10.4.1** The implementation MUST produce VAP Receipt v3.0 records that satisfy the field requirements in §8.1.

**10.4.2** The implementation MUST support offline verification of receipts.

**10.4.3** The implementation MUST compute and report coverage per §8.2.

### 10.5 Reporting

**10.5.1** The implementation MUST produce an Agentic Loss Run per §9.

**10.5.2** The implementation MUST include a random sample of receipts in Section D.

**10.5.3** The implementation MUST produce the report at the cadence specified in §9.3.

### 10.6 Test suite

**10.6.1** The implementation MUST pass the ARAS conformance test suite (published alongside this standard).

**10.6.2** The test suite covers: covariate normalization, dimension aggregation, composite scoring, tier mapping, and report generation.

**10.6.3** An implementation that passes the test suite MAY claim "ARAS v1.0 Conformant."

---

## 11. Governance and Maintenance

### 11.1 Stewardship

ARAS is published as an open standard. Stewardship is delegated to a neutral body (candidate bodies: Linux Foundation, IETF, or a new consortium formed for this purpose). The steward is responsible for:

- Maintaining the specification.
- Publishing the conformance test suite.
- Reviewing proposed changes.
- Releasing version updates.

### 11.2 Change process

Changes to ARAS follow a public comment process:

1. Proposal published as a pull request to the standard's repository.
2. 30-day public comment period.
3. Steward review and decision.
4. If accepted, released as a minor (v1.x) or major (v2.0) version.

### 11.3 Calibration roadmap

ARAS v1.0 uses expert-judgment weights. The roadmap for calibration:

- **v1.1 (Q2 2027):** Incorporate first loss experience from design partners.
- **v1.5 (Q4 2027):** Recalibrate weights against 12 months of loss data.
- **v2.0 (Q2 2028):** Introduce actuarial models (frequency-severity, credibility theory) as an optional extension.

### 11.4 Compatibility

ARAS is designed to be compatible with:
- WEF ACAP (ARAS covariates map to ACAP sections; crosswalk in Appendix C).
- OWASP Securing Agentic Applications v2 (ARAS behavioral covariates map to OWASP threat taxonomy).
- VAP Receipt v3.0 (ARAS evidence requirements are expressed in VAP fields).

---

## Appendix A: Normalization Functions and Weights

### Table A.1 — Authority Risk weights and normalization

| Covariate | Weight | Normalization |
|---|---|---|
| A1 Spend limit | 0.20 | `min(value / 10,000 EUR, 1.0)` |
| A2 Allowlist breadth | 0.10 | `min(count / 100, 1.0)` |
| A3 Category breadth | 0.10 | `min(count / 10, 1.0)` |
| A4 Mandate duration | 0.15 | `min(days / 365, 1.0)` |
| A5 Delegation depth | 0.15 | `min((depth - 1) / 3, 1.0)` |
| A6 Revocation capability | 0.10 | `1.0` if false, `0.0` if true |
| A7 HITL threshold | 0.10 | `1.0 - (threshold / 5)` (higher tier = lower risk) |
| A8 Lethal Trifecta exposure | 0.10 | `1.0` if true, `0.0` if false |

### Table A.2 — Evidence Risk weights and normalization

| Covariate | Weight | Normalization |
|---|---|---|
| E1 DQI composite | 0.25 | `1.0 - DQI_score` |
| E2 DQI components | 0.10 | `mean(1.0 - component_score)` |
| E3 Receipt completeness | 0.15 | `1.0 - completeness_fraction` |
| E4 Signature validity | 0.15 | `1.0` if single-signed, `0.0` if dual-signed |
| E5 Offline verification | 0.10 | `1.0` if failed, `0.0` if passed |
| E6 Pre-mandate commitment | 0.10 | `1.0` if absent or mismatched, `0.0` if matched |
| E7 Provenance completeness | 0.05 | `1.0 - provenance_fraction` |
| E8 Coverage | 0.10 | `1.0 - coverage_fraction` |

### Table A.3 — Behavioral Risk weights and normalization

| Covariate | Weight | Normalization |
|---|---|---|
| B1 Block rate | 0.20 | `min(block_rate / 0.10, 1.0)` |
| B2 Human override rate | 0.15 | `min(override_rate / 0.20, 1.0)` |
| B3 HITL latency | 0.10 | `min(p95_hours / 24, 1.0)` |
| B4 Escalation rate | 0.10 | `min(escalation_rate / 0.30, 1.0)` |
| B5 Novelty score | 0.15 | `mean_novelty` |
| B6 Revocation rate | 0.05 | `min(revocation_rate / 0.10, 1.0)` |
| B7 Incident count | 0.15 | `min(weighted_incidents / 5, 1.0)` |
| B8 Drift signals | 0.10 | `min(drift_count / 10, 1.0)` |

### Table A.4 — Environment Risk weights and normalization

| Covariate | Weight | Normalization |
|---|---|---|
| N1 Deployment tier | 0.30 | L1=0.2, L2=0.6, L3=1.0 |
| N2 Jurisdiction | 0.20 | EU=0.3, US=0.3, UK=0.3, other=0.6 |
| N3 Data classification | 0.25 | public=0.1, internal=0.4, confidential=0.7, restricted=1.0 |
| N4 Tool capability class | 0.15 | API=0.2, code=0.6, DB=0.5, web=0.7, PC=0.9, critical=1.0 |
| N5 Regulatory regime | 0.10 | minimal=0.2, limited=0.5, high-risk=0.9 |

### Dimension weights (§6.5)

- `w_A = 0.30`
- `w_E = 0.30`
- `w_B = 0.25`
- `w_N = 0.15`

---

## Appendix B: Worked Examples

### Example B.1 — Low-risk agent

**Agent:** Customer service agent at a retail company. Authorized to offer discounts up to 10%, no spending authority. Deployed on L1 (single-org), EU jurisdiction, internal data only, API tool class.

**Covariates (excerpt):**
- A1 spend limit: 0 (normalized 0.0)
- A4 mandate duration: 30 days (0.08)
- A5 delegation depth: 1 (0.0)
- A8 Lethal Trifecta: false (0.0)
- E1 DQI: 0.92 (0.08)
- E4 dual-signed: yes (0.0)
- B1 block rate: 1% (0.10)
- B5 novelty: 0.15 (0.15)
- N1 deployment tier: L1 (0.20)
- N3 data: internal (0.40)
- N5 regulatory: limited (0.50)

**Computed scores:**
- Authority Risk: 0.05
- Evidence Risk: 0.06
- Behavioral Risk: 0.12
- Environment Risk: 0.28

**Composite:** `0.30×0.05 + 0.30×0.06 + 0.25×0.12 + 0.15×0.28 = 0.015 + 0.018 + 0.030 + 0.042 = 0.105`

**Tier:** 1 (Minimal).

**Underwriting implication:** Standard rates. Annual reporting. Standard coverage.

### Example B.2 — High-risk agent

**Agent:** Procurement agent at a multinational. Authorized to spend up to €50,000 with 200 approved suppliers. Deployed on L3 (multi-platform), mixed jurisdictions, confidential data, web + DB tool class.

**Covariates (excerpt):**
- A1 spend limit: €50,000 (normalized 1.0)
- A2 allowlist: 200 (1.0)
- A4 mandate duration: 365 days (1.0)
- A5 delegation depth: 3 (0.67)
- A8 Lethal Trifecta: true (1.0)
- E1 DQI: 0.65 (0.35)
- E4 dual-signed: yes (0.0)
- B1 block rate: 8% (0.80)
- B5 novelty: 0.55 (0.55)
- B7 incidents: 3 (0.60)
- N1 deployment tier: L3 (1.0)
- N3 data: confidential (0.70)
- N5 regulatory: high-risk (0.90)

**Computed scores:**
- Authority Risk: 0.88
- Evidence Risk: 0.38
- Behavioral Risk: 0.62
- Environment Risk: 0.87

**Composite:** `0.30×0.88 + 0.30×0.38 + 0.25×0.62 + 0.15×0.87 = 0.264 + 0.114 + 0.155 + 0.131 = 0.664`

**Tier:** 4 (Elevated) — escalated from 3 due to incident history.

**Underwriting implication:** High rates. Board sign-off required. Monthly reporting. Coverage available with restricted limits.

### Example B.3 — Blocked over-spend event

**Context:** Agent authorized for €200 to Supplier A. Agent attempts €350 to Supplier B. Execution gate blocks the transaction.

**Receipt fields:**
- `mandate.scope.spend_limit`: €200
- `action.attempted_amount`: €350
- `execution_status`: `blocked`
- `block_reason`: `spend_limit_exceeded`
- `constraints_applied`: `[{rule_id: "spend_limit", actual_value: 350, expected_value: 200, compliant: false}]`
- `commerce_dqi.score`: 0.88

**ARAS scoring:**
- This action contributes to B1 (block rate) as one blocked action.
- The receipt's DQI contributes to E1.
- The action does NOT count as an incident (it was successfully blocked).
- The agent's authority is reassessed: if block rate rises, Tier escalates.

**Underwriting implication:** This is evidence of the control working, not evidence of loss. It should be reported in Section B (block rate) but not Section C (incident log). The underwriter treats a blocked action as a positive signal about the execution gate, not a negative signal about the agent.

---

## Appendix C: Crosswalk to WEF ACAP and OWASP

### C.1 ARAS to WEF ACAP

| ARAS dimension | ACAP section |
|---|---|
| Authority Risk | Section C (Authority and consequential events) |
| Evidence Risk | Section D (Controls and enforcement) + Section F (Monitoring) |
| Behavioral Risk | Section F (Monitoring) + Section G (Re-authorization) |
| Environment Risk | Section B (Operating context) |

ARAS operationalizes ACAP Sections B–G as measurable covariates. An ACAP-compliant deployment that also implements ARAS gains quantitative risk scoring without changing its governance structure.

### C.2 ARAS to OWASP

| ARAS covariate | OWASP reference |
|---|---|
| A8 Lethal Trifecta exposure | §2.7.3 (Capability Composition Risk) |
| B8 Reasoning drift | §3.3.1 (Continuous Monitoring) |
| E3 Receipt completeness | §3.3.7 (Hooks as enforcement points) |
| E5 Offline verification | §9.4 (Observability + Forensics) |
| N4 Tool capability class | §5.0 (Capability Control Matrix) |

ARAS assumes an OWASP-compliant security posture. The two standards are complementary: OWASP defines the controls, ARAS measures their effectiveness.

---

## Appendix D: Glossary

**Agent.** An autonomous software system that acts on behalf of a human or organization.

**ARAS.** Agentic Risk Assessment Standard.

**Composite Score.** The weighted combination of four dimension scores, ranging 0.0–1.0.

**Coverage.** The fraction of agent actions for which a conforming receipt exists.

**DQI.** Data Quality Index; composite measure of evidence adequacy.

**Execution Gate.** Runtime enforcement point that checks actions against mandates.

**Loss Run.** Standardized report of agent activity and incidents submitted to insurers.

**Mandate.** Cryptographically signed authorization for an agent's actions.

**Pre-Mandate Commitment.** Hash commitment to human intent made before agent proposal.

**Receipt.** Cryptographically signed record of an agent action.

**Tier.** One of five risk classifications (Minimal to Critical).

---

## Appendix E: Agentic Loss Run JSON Schema (Abbreviated)

```json
{
  "$schema": "https://aras.standard/v1.0/loss-run.schema.json",
  "type": "object",
  "required": ["aras_version", "reporting_period", "agent_inventory", "aggregate_metrics", "incident_log", "evidence_sample"],
  "properties": {
    "aras_version": {"type": "string", "enum": ["1.0"]},
    "reporting_period": {"type": "object", "properties": {"start": {"type": "string", "format": "date-time"}, "end": {"type": "string", "format": "date-time"}}},
    "agent_inventory": {"type": "array", "items": {"$ref": "#/definitions/AgentInventory"}},
    "aggregate_metrics": {"type": "array", "items": {"$ref": "#/definitions/AggregateMetrics"}},
    "incident_log": {"type": "array", "items": {"$ref": "#/definitions/Incident"}},
    "evidence_sample": {"type": "array", "items": {"$ref": "#/definitions/Receipt"}}
  },
  "definitions": {
    "AgentInventory": {"type": "object", "required": ["agent_id", "name", "version", "deployment_tier", "aras_score", "aras_tier", "coverage"], "properties": {"agent_id": {"type": "string"}, "name": {"type": "string"}, "version": {"type": "string"}, "deployment_tier": {"type": "string", "enum": ["L1", "L2", "L3"]}, "aras_score": {"type": "number", "minimum": 0, "maximum": 1}, "aras_tier": {"type": "integer", "minimum": 1, "maximum": 5}, "coverage": {"type": "number", "minimum": 0, "maximum": 1}}},
    "AggregateMetrics": {"type": "object", "required": ["agent_id", "total_actions", "block_rate", "human_override_rate", "hitl_latency_median_hours", "hitl_latency_p95_hours", "dqi_mean", "dqi_p10", "dqi_p90"], "properties": {"agent_id": {"type": "string"}, "total_actions": {"type": "integer"}, "block_rate": {"type": "number"}, "human_override_rate": {"type": "number"}, "hitl_latency_median_hours": {"type": "number"}, "hitl_latency_p95_hours": {"type": "number"}, "dqi_mean": {"type": "number"}, "dqi_p10": {"type": "number"}, "dqi_p90": {"type": "number"}}},
    "Incident": {"type": "object", "required": ["incident_id", "timestamp", "agent_id", "type", "severity", "receipt_ref"], "properties": {"incident_id": {"type": "string"}, "timestamp": {"type": "string", "format": "date-time"}, "agent_id": {"type": "string"}, "type": {"type": "string", "enum": ["T1", "T2", "T3", "T4", "T5", "T6", "T7", "T8", "T9", "T10", "T11", "T12", "T13", "T14", "T15"]}, "severity": {"type": "string", "enum": ["low", "medium", "high", "critical"]}, "root_cause": {"type": "string"}, "remediation": {"type": "string"}, "receipt_ref": {"type": "string"}}},
    "Receipt": {"type": "object", "description": "VAP Receipt v3.0 per SPEC-RECEIPT-v3.0"}
  }
}
```

---

**End of ARAS v1.0**

*This standard is published for public review and adoption. Comments, corrections, and proposed amendments should be submitted to the steward body identified in §11.1.*

*The reference implementation of ARAS is available as open-source software under the MIT license. The conformance test suite is available under CC BY-SA 4.0.*