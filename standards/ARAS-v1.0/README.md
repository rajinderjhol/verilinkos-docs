Agentic Risk Assessment Standard (ARAS) v1.0
Status: Draft for Public Review
License: CC BY-SA 4.0

The Problem
Insurers cannot underwrite a risk nobody has quantified. Today, agentic risk is treated as uninsurable because there is no standardized way to measure agent behavior, evidence quality, or authority scope.

The Solution
ARAS is a proposed measurement standard for autonomous AI agents. It defines the covariate set, the scoring methodology, and the risk tier classification required for liability underwriting. It bridges the gap between the WEF's ACAP governance framework and the OWASP's security execution controls.

The measurement layer
ARAS is not just a covariate list. Its Evidence Risk dimension is computed from a seven-component Data Quality Index (DQI) — a standardized measurement of whether a receipt is pricing-grade evidence or just a cryptographically valid log. The DQI is what turns a signed receipt into an actuarial variable. See §5.2.1 for component definitions.

Status of this draft
This repository currently contains the normative standard (ARAS-v1.0.md) and this README. The covariate set (§5), scoring methodology (§6), tier classification (§7), and DQI component definitions (§5.2.1) are normative in this draft. Two components referenced in the standard are not yet published here: (a) the conformance test suite (§10.6), in development and planned for publication alongside revision 3; (b) the Agentic Loss Run JSON schema (Appendix E), currently a draft schema embedded in the standard, subject to change before v1.0-final.

Why this exists
VeriLinkOS is building a reference implementation of the technical evidence layer (VAP Receipts) that makes ARAS measurable. But for the insurance market to scale, the measurement standard must be open, neutral, and adopted across vendors and carriers. ARAS is published under CC BY-SA 4.0 and is intended to be stewarded by a neutral standards body (§11.1), not by any single implementation vendor.

How to engage
- Review the standard: Read ARAS-v1.0.md (approximately 30 minutes).
- Submit feedback: Open an issue or pull request against this repository. Per §11.2, proposals undergo a 30-day public comment period.
- Adopt the standard: Section 10 defines the conformance requirements. An implementation that passes the conformance test suite (§10.6) MAY claim "ARAS v1.0 Conformant."
- Contribute to stewardship: ARAS is intended to be stewarded by a neutral standards body (§11.1). Expressions of interest from candidate bodies are welcome.

Read the full standard: ARAS-v1.0.md
