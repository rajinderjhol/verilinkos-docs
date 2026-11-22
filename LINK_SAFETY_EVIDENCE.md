# Link Safety evidence and limits

Last reviewed: 2026-11-22

## What is measured

`conformance/vectors/link_safety_vectors.json` is a **12-item synthetic corpus**
run by `conformance/run_conformance.py link`. It measures whether the current
heuristic engine produces the expected verdict for a small set of hand-written
URLs, with network unfurling disabled so the result is deterministic and offline.

Latest result: **13/13 checks passed** (12 vectors plus the metrics row), with
precision 1.00, recall 1.00 and false-positive rate 0.00 — **on this corpus**.

## What that number does not mean

- The corpus was written alongside the rules it tests. It is a regression net,
  not an independent benchmark.
- Twelve URLs cannot estimate real-world phishing precision or recall. It
  contains no live Threat Intelligence, no aged domains, no homograph attacks,
  no shortened redirect chains and no adversarial evasion.
- A false-positive rate of 0.00 here does not imply a low false-positive rate
  in production. Real brand and path corpora are needed before that claim.
- **No premium discount, underwriting tier or pricing decision is supported by
  this evidence.** The engine's "tier" label is an advisory evidence tier only.

## What the decision artifact does prove

Each check produces a signed advisory decision (`LNK-*`):
- a canonical hash of the exact decision payload,
- an Ed25519 signature over that hash by the deployment authority key,
- threat-intel signals with `source`, `confidence` and `observed_at`,
- the full redirect chain and the reason if safe unfurling stopped.

`GET /v1/link-safety/decisions/{id}/bundle` exports a signed ZIP containing the
decision, both public keys and a manifest signed by the same root key. Run:

```bash
.venv/bin/python scripts/verify_decision_bundle.py --bundle <decision>.zip
```

This verifies bundle integrity and the decision signature offline, with no
account or platform trust. It proves the decision was not altered — not that
the verdict was correct.

## Known limits

- Detection is heuristic: brand substrings, a small TLD list, keyword matching.
- No external reputation feed is enabled by default; `HttpIntelProvider` is
  opt-in and any signal it returns carries its source and timestamp.
- QR decoding happens in the browser via `BarcodeDetector`; browsers without
  that API must paste decoded text.
- The safe-unfurl check blocks non-global destinations per hop and honours
  `max_redirects`, but a connecting HTTP proxy remains the stronger SSRF control.
- Decisions are advisory. Nothing silently blocks execution because of them.

## Forward-looking: the path to an insurance claim

The insurance direction is intentional. The evidence layer is shipped; the
underwriting product is not. Each gate below must be met before a higher claim
is made:

1. **Labelled real-world corpus** — real incidents and legitimate traffic,
   including homograph and redirect-chain cases, with held-out evaluation.
2. **Published calibration** — per-rule false-positive/false-negative rates and
   confidence calibration, not just a synthetic regression count.
3. **Outcome linkage** — record whether a flagged link was actually harmful,
   bound to the same signed decision.
4. **Loss-correlation validation** — a carrier partnership and a longitudinal
   study showing the vectors predict claims.
5. **External review** — replication by a party that did not write the rules,
   plus an auditable published methodology.

Only after those gates could premium differentiation or portfolio use be
discussed. Until then the honest claim is "regression-tested heuristic signals
with verifiable decision provenance," not "measured protection" and not a
pricing capability. The dashboard's "Underwriting signals" panel shows the
advisory inputs explicitly labelled for what they are.
