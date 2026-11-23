# Risk exposure and the loss model — current state and gaps

> This page states what the code actually computes today. Where it conflicts
> with older whitepapers or blog posts, the current implementation and
> [STATUS.md](STATUS.md) win. No insurance, underwriting or financial claim here
> is external certification. PersonaVault is a separate system with no defined
> interoperation.

## Bottom line

**There is no validated loss model and no realized-loss base rate in either system.**
A currency-denominated "risk exposure" figure would therefore be invented. The
Guardian console shows `—` with "No valuation model connected" until a real model
exists. ARAS is explicitly *a measurement standard, not a pricing standard*.

Three different quantities are easy to confuse:

| Term | What it actually is here | Currency? |
|---|---|---|
| Premium | A deterministic rate applied to a **user-supplied** coverage amount | USD, but illustrative |
| `risk_exposure` (insurance analytics) | `mean(premium / coverage)` over persisted policies — a rate, not a loss | No |
| Risk exposure (Guardian card) | Not computed — displayed as `—` | No |
| ARAS composite | Advisory risk tier from covariates; missing inputs scored worst-case | No |
| Loss ratio | Not computed by the backend; UI shows `—` | No |

## What exists in code

### 1. Persisted underwriting service (real, DB-backed)
`verilink/services/insurance_underwriting.py` + `verilink/routers/insurance.py`.

- `trust_score(agent)` in `commerce_ledger.py`: `clamp(mcp_reputation_score, 0, 1)`,
  `+0.05` if the agent resolves to a human owner, capped at 1.
- `_premium`: `rate = (0.03 + (1 − trust) × 0.10) × term_days/30`;
  `premium = coverage × rate`; `deductible = coverage × (0.005 + (1 − trust) × 0.02)`.
- Quotes, policies and claims persist in `insurance_quotes`, `insurance_policies`,
  `insurance_claims`. `get_analytics()` sums real coverage, premiums and paid
  claims; `get_triggers()` derives triggers from real policy/claim state.
- **Status:** the inputs are real; the coefficients (0.03, 0.10, 0.005, 0.02) are
  unvalidated assumptions. Coverage is supplied by the operator, never inferred
  from assets.

### 2. Premium pricing engine (illustrative)
`verilink/insurance/pricing_engine.py`.

- Base rates by coverage type: 0.04–0.15 of coverage.
- Composite risk weights: quality 0.30, security 0.20, compliance 0.25,
  cost 0.15, history 0.10. Multi-hop uses the joint product rule
  `1 − Π(1 − R_i)` and trust factor from the 5D trust vector.
- Its router (`verilink/insurance/routes.py`) is **not registered** in `main.py`;
  only `POLICY_STORE` is used as a mirror. Two call sites still use a stale
  constructor signature.
- ZK range "proofs" in `zk_snarks.py` are labelled SHA256 hash simulations,
  not production SNARKs.

### 3. Parametric claims
`verilink/insurance/claims.py`.

- Payout is hardcoded at `coverage_limit × 0.75` for a security breach; the more
  granular `trigger_rules` table is defined but not used for payout selection.
- Multi-hop loss allocation is hardcoded 70% leaf / 30% delegator.
- Claims route through Guardian enforcement. These are deterministic demo
  payout rules, not actuarial loss estimates.

### 4. Partner reinsurance
`verilink/insurance/partner_api.py` uses a hardcoded 2% of ceded amount.

### 5. ARAS v1.0
`verilink/services/aras.py` and `standards/ARAS-v1.0/ARAS-v1.0.md`.

- Four dimensions (Authority 0.30, Evidence 0.30, Behavioral 0.25,
  Environment 0.15), 29 covariates; `null` scores worst case (1.0).
- Five tiers. The standard states weights are "initial estimates based on
  expert judgment"; calibration roadmap targets v1.1 (first loss experience),
  v1.5 (recalibration on 12 months of loss data), v2.0 (frequency-severity /
  credibility models).
- Conformance self-tests pass 37/37, but these are **not** certification and
  loss correlation remains "an open empirical question".

### 6. PersonaVault
No financial valuation engine. The insurance domain pack's policies are all
`autonomy.level: recommend` and its persona says "Never override a human
decision; only recommend and escalate". DQI, replay, calibration and outcome
linkage are advisory and cannot change signatures, integrity verdicts or
approvals. The only enforced scores are answer-gating coverage/faithfulness
checks, unrelated to valuation.

## Runtime honesty fixes already applied

- `POST /v1/verify/insurance/quote/generate` no longer emits a named carrier,
  a "$5,000,000" limit, a "$10,000" deductible, 15–30% discounts or
  `BINDING_QUOTE_READY`. It returns `ILLUSTRATIVE_NOT_BINDING`,
  `binding: false`, `advisory_only: true`, an explicit formula, its unvalidated
  coefficient status, and a `not_claimed` list. Supplied reputation is labelled
  unverified and no VAP proof is asserted.
- The dashboard no longer sends a hardcoded `reputation_score: 0.9`; it derives
  reputation from real agents or refuses to produce an indication. The user
  supplies the base premium explicitly.
- The "binder" modal is now an "Illustrative Premium Indication"; the fake
  "Bind & Download Policy (PDF)" button was removed — it previously created a
  DB policy with default values and produced no PDF.
- Insurance analytics no longer substitute `0%` / `$0` for missing values; they
  show `—`. The KPI previously labelled "Avg Risk" is now correctly labelled
  "Premium/Coverage Ratio".
- The Guardian console no longer shows a fabricated CHF liability figure,
  `100%` fallbacks on empty data, or hardcoded compliance checkmarks.

## Gaps to production-quality readiness

1. **Asset valuation.** There is no signed inventory of asset criticality or
   value. Coverage is a free-form input. Without it, exposure in currency is
   undefined.
2. **Outcomes ledger.** No link from a receipt to an incident to a realized
   amount. `SecurityIncident` carries severity only; claims carry a user-declared
   amount. Frequency and severity base rates cannot be estimated.
3. **Loss correlation.** ARAS covariates are extractable, but whether they
   predict loss frequency or severity is unvalidated. This requires a carrier
   partnership and a longitudinal study; it cannot be produced locally.
4. **Coefficient validation.** All rate/multiplier constants above are expert
   guesses. They need backtesting against realized outcomes and documented
   revision control.
5. **Uncertainty.** Point estimates must become ranges with confidence, and
   "insufficient data" must remain a first-class output.
6. **Code hygiene.** The unregistered insurance router, stale call signatures,
   and hardcoded payout/allocation rules need to be removed or made explicit.
7. **Advisory enforcement.** Scores must never enter approval, integrity or
   signing paths. PersonaVault demonstrates the right boundary; VeriLinkOS
   underwriting must keep it.
8. **External validation.** Carrier partnership, regulator review, external
   audit and multiple-carrier data sharing are the final gates before any
   portfolio, reinsurance or pricing claim.

## Pre-Stage-0 gap register

This is the precondition analysis, recorded before any Stage 0 code was written.
Items marked **blocking** must be satisfied by Stage 0; the rest are later gates.

### Documentation

| Gap | Detail | Blocking |
|---|---|---|
| No loss-model specification | ARAS v1.0 defines measurement, not loss estimation. The whitepaper pricing formula `P = Base_Rate × Coverage × Integrity × Control × Change × State` is a proposal, not an implemented model. No document defines a loss event, frequency/severity estimation or calibration method. | Yes |
| ARAS artefacts incomplete | Appendix E loss-run schema is draft; the conformance suite is described as "planned for a future revision" although 37 local vectors exist. The v1.1/v1.5/v2.0 calibration roadmap has no local counterpart. | Yes |
| No model-card / change-control policy | No documented rule for who approves a coefficient change, how it is signed, or how a historical decision is re-scored against its exact model version. | Yes |
| No calibration methodology | No Brier score, reliability diagram or confidence-interval method documented for any score. | No |
| DQI / insurance tension | PersonaVault docs call DQI "marketable to insurers" while STATUS.md correctly holds DQI as advisory and not decision correctness. The bridge is unwritten. | No |
| Undefined loss attribution | No documented rule for what counts as a loss event or how a realized amount is attributed across a multi-hop chain. | Yes |

### Code

| Gap | Detail | Blocking |
|---|---|---|
| No loss-observation model | `SecurityIncident` has severity but no amount; claims carry a user-declared amount. No signed, org-scoped incident→amount record. | Yes |
| No outcomes ledger | No link from a receipt to an incident and a realized amount. PersonaVault has `OutcomeReceiptPayload`; VeriLinkOS has no equivalent. | Yes |
| No model registry / version pinning | No signed list of model versions with coefficients and approver; scores cannot be reproduced against the version that produced them. | Yes |
| No signed score attestation or offline verifier | No portable artifact binding inputs, model version and output signature. | Yes |
| Hardcoded financial rules | Payout `coverage × 0.75`, 70/30 loss allocation, 2% reinsurance, unvalidated underwriting coefficients — none versioned or signed. | No |
| Dead / duplicate code | `verilink/insurance/routes.py` is unregistered, has stale `ParametricClaimsEngine` call signatures; `partner_api.py` imports `POLICY_STORE` from it. | No |
| ZK simulation labelling | `zk_snarks.py` uses SHA256 commitments; labelled in tests but not consistently in runtime responses. | No |
| No calibration computation | No Brier/reliability calculation and no first-class `insufficient_data` result in financial scoring paths. | Yes |
| Unverified quote input | The quote endpoint accepts caller-supplied reputation; now labelled unverified but not validated. | No |

### DQI (both systems)

| Gap | Detail | Blocking |
|---|---|---|
| Weights never outcome-linked | DQI is advisory and frozen into receipts, but weights are expert estimates. PersonaVault's `outcome_validation` writes only `validation_status` / `derived_from.validations`, never weights. VeriLinkOS has no equivalent. | No |
| No DQI → loss evidence | ARAS uses `E1 = 1 − DQI` as an actuarial covariate, but DQI measures documentation quality, not decision correctness or loss. The mapping is unvalidated. | No |
| No DQI model card | No signed approval or version history for weight changes between v1 and v2. | No |
| Verifier coverage | Frozen snapshot binding is enforced in PersonaVault `verify_cdr.py`; equivalent VeriLinkOS offline-verifier coverage must be confirmed before DQI is reused for loss scoring. | No |

### VAP

| Gap | Detail | Blocking |
|---|---|---|
| No outcome / loss fields | VAP receipts carry decisions and evidence, not realized outcomes or amounts. | Yes |
| No score artifact type | No `ScoreAttestation` or loss-run artifact in the receipt schema. | Yes |
| No model identity in receipt | DQI snapshot exists, but no model-version field identifying the scorer. | Yes |
| Pre-execution intent commitment | Still forward-looking; blocks ARAS E6 / anti-drift and any claim that covariates were captured before the decision. | No |
| No financial-decision receipt type | Governance classifies quote tier 2 and issue/reinsure/claim tier 3, but no dedicated signed receipt for underwriting decisions. | No |
| Version boundary | Runtime emits `version = "3.0"`; v3.5 is forward-looking. The loss model must not depend on unshipped fields. | Yes |

### Other

| Gap | Detail | Blocking |
|---|---|---|
| No realized loss data / base rates | The fundamental blocker; external by nature. | No (external gate) |
| No carrier partnership / real corpus / audit / regulator review | Required before any pricing or portfolio claim. | No (external gate) |
| No accountable human owner | No signed approval path for model changes. | Yes |
| No privacy / selective disclosure design | Sharing a loss run with counterparties may reveal operational detail. | No |
| Sparse deployments | Most collections are empty, so early backtests will be low-data; the ledger must report `insufficient_data` honestly. | Yes |

### Stage 0 scope decision

**Build now:** loss-observation model, outcomes ledger, model registry with signed approval, signed score attestation, offline verifier, `insufficient_data` semantics, and the spec/model-card documentation that defines them.

**Defer:** coefficient calibration, carrier partnership, external audit, pricing/discount claims, multi-carrier portfolio use, v3.5 fields.

VAP, DQI freezing and advisory boundaries are already solid foundations. Stage 0 adds new artifacts around them rather than reworking them.

## Stage 0 — shipped (2026-11-23)

Stage 0 is implemented as a set of new, signed artifacts around the existing
VAP/DQI substrate. It adds no predictive or pricing capability.

### What is now available

- `POST /v1/loss/observations` — org-scoped, Ed25519-signed loss observation
  (event type, severity, realized USD amount, linked receipt IDs, source).
  `source` may be `real_action`, `manual_entry` or `simulation`; simulations are
  excluded from scoring.
- `POST /v1/loss/models` — admin-only registration of a signed methodology
  version. The only current version kind is `descriptive`, with four parameters:
  `min_observations`, `min_period_days`, `low_percentile`, `high_percentile`.
- `POST /v1/loss/score` — produces a signed attestation over the observation
  set. The output is **descriptive**: observation count, period, observed
  frequency, observed mean severity and an interpolated low/high range — or
  `insufficient_data: true` with a reason when thresholds are not met.
  Every result carries `advisory_only: true` and `predictive: false`.
- `GET /v1/loss/observations`, `GET /v1/loss/models`, `GET /v1/loss/score`,
  `GET /v1/loss/bundle` — read and export.
- `scripts/verify_loss_bundle.py` — standalone offline verifier (exit 0 / 7)
  that checks the manifest signature and hash, all file hashes, the model and
  score signatures, the score→model and score→input bindings, and **recomputes**
  the descriptive profile from the bundled observations. It rejects a tampered
  score and any bundle that marks a descriptive score as predictive.

### Accountability, verifiability, scorable, valuable

| Property | How Stage 0 satisfies it |
|---|---|
| Accountable | Admin-only model registration; every artifact signed with the authority Ed25519 key; approver identity recorded; model version bound into each score. |
| Verifiable | Deterministic shared profile function; offline verifier recomputes the score from bundled inputs and parameters without network or platform trust. |
| Scorable | Real aggregates over signed observations, expressed as a range, with `insufficient_data` as a first-class result rather than a zero. |
| Valuable | Usable immediately as an auditable loss-run ledger and control-prioritisation input — before any prediction, premium or pricing claim. |

### Boundaries held

- No probability of loss, premium, discount, coverage limit or underwriter is
  produced by Stage 0.
- No approval, enforcement, receipt-integrity or signing path reads a loss score
  (enforced by a regression test that asserts those modules contain no consumer).
- The pre-Stage-0 gap register above remains the roadmap; calibration, carrier
  partnership, external audit and pricing stay in later gates.

### Verification

- `tests/test_loss_models.py`: 5 tests — signed registration, honest
  insufficient-data result, exclusion of simulated observations, offline bundle
  verification plus tamper detection, and the advisory-boundary assertion.
- Broader focused run: 18 passed across loss models, Guardian and insurance.
- Full VeriLinkOS suite: 1026 passed, 29 skipped, 1 xfailed (2026-11-23).
- Agent Roster scorecards restored with real data; 25 focused tests passed across
  loss models, dashboard actions, enforcement coverage, governance drift,
  Guardian and insurance.
- Live end-to-end smoke: model 201, three observations 201, score 201 with
  `insufficient_data: false`, range 120–280, `advisory_only: true`,
  `predictive: false`, bundle 5353 bytes.
- Alembic revision `20261123000001` applied; dev database at head.

## Status wording to use

- "Deterministic premium indication from a caller-supplied coverage amount and
  real reputation signals; coefficients are unvalidated."
- "Advisory covariates; no premium, discount or coverage decision is claimed."
- "No validated loss model; exposure shown as unavailable."
- Forward-looking only (not present as shipped or Roadmap): premium
  differentiation, underwriting tiers used for pricing, portfolio/reinsurance
  use, carrier partnerships, loss correlation, actuarial calibration, and
  external certification.
