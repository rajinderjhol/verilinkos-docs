# Conformance status

Last reviewed: 2026-11-22

## What this is

VeriLinkOS ships an **offline conformance vector suite** for three surfaces:

| Surface | Vectors | What they check |
|---|---|---|
| A2A v1.0 (gateway subset) | 5 | Supported JSON-RPC methods, error code for unknown methods, complete task-state set, terminal states, legal/illegal transitions |
| MCP JSON-RPC 2.0 (2026-07-28) | 6 | Advertised spec version, server-card/method consistency, standard error codes, request envelope parsing, supported core methods |
| ARAS v1.0 | 13 | Covariate normalization (bounded/fractional/boolean/categorical), direction inversion for DQI covariates, null-worst-case penalty, dimension aggregation, composite weighting, tier/band boundaries |
| Link Safety (synthetic corpus) | 13 | 12 labelled URL vectors plus precision/recall/false-positive metrics; deterministic offline with unfurling disabled |

Run them locally:

```bash
cd VeriLinkOS-main
.venv/bin/python conformance/run_conformance.py
```

The suite is also wired into pytest as `conformance/test_conformance.py`, so it
runs with the full VeriLinkOS test suite. Result at last review: **37/37 vectors passed**.
The Link Safety corpus is synthetic and cannot support real-world protection or
underwriting claims; see [LINK_SAFETY_EVIDENCE.md](LINK_SAFETY_EVIDENCE.md).

The ARAS reference implementation is `verilink/services/aras.py`; the normative
source is `standards/ARAS-v1.0/ARAS-v1.0.md` §6–§7 and Appendix A. The same
scorer backs the advisory endpoint `POST /v1/analytics/aras/report`, which adds
covariate extraction from real receipts and explicit deployment facts. Missing
covariates stay `null` and are scored worst-case; the endpoint never gates
approval or integrity.

## Status of external claims

**Not certified.** These vectors are self-tests against the local implementation.
They are not an audit, an accreditation, or a certification by any standards body.
In particular:

- We do **not** claim external A2A conformance certification.
- We do **not** claim MCP certification by any authority (the spec is published by
  its maintainers; we implement a documented subset).
- We do **not** claim ARAS validation against a book of real claims. ARAS v1.0 is a
  *proposed* measurement methodology; its covariates are extractable from a VAP
  receipt, but whether they correlate with loss is an open empirical question.
  See the whitepaper's validation program.
- We do **not** claim W3C DID interoperability or SCITT conformance.

Claiming any of the above remains a separate decision that requires an external
audit and a public record, not just a green local test.

## Known spec ambiguity

ARAS §6.2 describes distribution normalization as `1.0 - (p95 / reference_max)`,
while Appendix A Table A.3 gives `min(p95_hours / 24, 1.0)` for B3. The conformance
reference follows the Appendix A table (the specific, authoritative normalization
table). This ambiguity is recorded here rather than papered over.
