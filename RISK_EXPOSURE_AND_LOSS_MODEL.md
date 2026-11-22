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

## Status wording to use

- "Deterministic premium indication from a caller-supplied coverage amount and
  real reputation signals; coefficients are unvalidated."
- "Advisory covariates; no premium, discount or coverage decision is claimed."
- "No validated loss model; exposure shown as unavailable."
- Forward-looking only (not present as shipped or Roadmap): premium
  differentiation, underwriting tiers used for pricing, portfolio/reinsurance
  use, carrier partnerships, loss correlation, actuarial calibration, and
  external certification.
