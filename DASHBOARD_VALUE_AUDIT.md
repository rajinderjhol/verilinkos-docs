# Dashboard value audit — do the tabs close a loop?

Last reviewed: 2026-11-22

## The test

A tab creates value only if it closes a loop:

1. **Input** — the user can put something real in.
2. **Artifact** — the system produces a signed, portable result.
3. **Verification** — the result can be checked independently.
4. **Next action** — export, revoke, review or feed another workflow.

A tab that only lists an empty collection, or ends with nothing to do, is a
dead end — regardless of how polished it looks.

## Classification (43 tabs)

### Closed loop — value today

| Tab | Input → artifact → verification → next action |
|---|---|
| Link Safety | URL/QR → signed `LNK-*` decision → offline bundle → review / blocklist |
| Trustable Links & QR | target URL → signed declaration → WebCrypto client check → QR / revoke |
| Agents | registration → generated Ed25519 keys → identity → capabilities / passport |
| Policies | rule creation → `/v1/policy/evaluate` decision → evidence → enable/disable |
| Compliance | framework → report/export → signed evidence → audit trail |
| Analytics / Calibration | outcome link → counterfactual replay report → advisory insight |
| Today / Command Center | telemetry events → action queue → governed decision |

### Conditional — real system, empty in this deployment

Live API counts at review: receipts **0**, A2A conversations **0**, escrows **0**,
incidents **0**, passports **0**, federation peers **0**, insurance policies **0**.

| Tab | What would activate it |
|---|---|
| VAP Ledger | any governed action that generates a receipt |
| A2A Communication | create a conversation / send a message |
| Deals & Negotiation | propose a deal |
| Digital Wallet / x402 Commerce | create an escrow / intent |
| Trust Passports | issue a passport |
| AI Federation | establish federation trust |
| Insurance Risks | request a quote / issue a policy |
| Dispute Resolution | file a dispute |
| Incident Response | report an incident |
| Asset Protection / Verification | register an asset |
| AI-BOM Explorer | generate an AIBOM |
| Flight Recorder | record a session |
| Trust Network / ZK Trust / Ontology | attest / prove / model data |
| Security Center | detect an incident |
| Remediation | generate a signed remediation envelope |

These tabs are not fake — the backends are real. But an end user sees an empty
table and a refresh button, which reads as a demo shell.

### Dead end — no loop

| Tab / panel | Why |
|---|---|
| Guardian HITL queue / circuit breakers / log | Explicit placeholders showing `—`; no queue exists |
| Remediation queue | `refreshRemediationData` is a no-op; only envelope generation works |
| Any empty list with only "Refresh" | No path from empty to populated that the user can see |

## What this means

The dashboard's problem is **breadth without density**. 43 tabs signal a large
product, but most cannot show an end user a completed loop. Value comes from
depth: a few tabs where a user can act, receive signed evidence and export it.

## Remediation plan

1. **Activation flow** — guide new users through register agent → create policy
   → check a link → verify a receipt → export a bundle. This generates real data
   (no fake seeding) and activates six tabs.
2. **Empty states with a CTA** — every empty collection states why it is empty
   and offers the action that fills it. Implemented for the top 10 tabs
   (`VLOps.emptyCTA` in `ops.js`).
2b. **Guided activation path** — the Today tab now shows five steps (register
   agent, create policy, check link, issue signed declaration, verify receipt),
   each with Done/Next/Pending badges derived from real endpoint data and a
   button that opens the corresponding workflow. No seeded or fake progress.
3. **Dormant tab grouping** — tabs with no data and no user action sit behind
   "More capabilities", labelled honestly.
4. **Artifact rule** — every tab ends in something downloadable, verifiable or
   actionable. If not, merge or remove it.
5. **Re-audit after activation** — repeat this classification once the
   activation flow has produced real data.

## Console workspaces (top navigation)

The four Consoles workspaces were audited separately and wired to real data:

| Workspace | Fix |
|---|---|
| Guardian | Removed `setDemoData()` and fabricated KPIs; pending HITL decisions now come from `/v1/policy/pending`; approve/deny call real `/v1/policy/*` endpoints. |
| Trust Center | Removed `Math.random()` trend and fabricated component scores; passports use `/v1/trust-passport/trust-passports`; reputation history uses the real endpoint. |
| Insurance | Removed hardcoded `1247 / 2.4M / 47 / 12` fallbacks and fabricated premiums; claims require user-supplied policy, amount and signed trigger. |
| Commerce | Replaced nonexistent `/v1/commerce/sandbox/simulate` with `/v1/commerce/execute`; removed fabricated x402/escrow/zk/premium stage values. |

All four now fail closed into honest empty/error states rather than painting
fake data.

## Human connection and journey — auth fixes

Tracing the human path from signup to offline verification found two P0 gaps,
both fixed:

- **Login no longer returns the deployment-wide `VERILINK_API_KEY`.** Browser
  authentication is the httpOnly session cookie; SDK keys are issued once at
  signup (per organisation) or explicitly via `/v1/admin/api-keys`. Profile
  settings now lists, creates and revokes keys.
- **No credentials in browser storage.** Login, signup and MFA no longer write
  API keys or JWTs to `localStorage`. `apiFetch` redirects on 401 *and* on
  auth-related 403s, with a "session expired" notice on the login page.

Those remaining gaps are now closed:

- **Consoles menu** is reachable from ≥1024 px with an explicit CSS rule, not
  only the compiled Tailwind variant.
- **Offline verification is a first-class step**: the activation-complete panel
  and the Trustable Links tab both offer "Download signed bundle" and
  "Download offline verifier" (`/v1/link-safety/verifier-script`).
- **`/verify` and `/trust`** carry an activation CTA into
  `/dashboard#tab=today`, and `/verify` explicitly distinguishes platform
  verification from offline bundle verification.
- **Credentials are explained** in Profile settings: session cookie, per-org
  SDK key and receipt signing key. Keys are bound to their creating human and
  support list / create / rotate / revoke.
- **Failure states** are user-visible via `VLOps.errorPanel` with retry.

## Honest status

The audit is a product assessment, not a technical defect list. The dead-end
tabs are not broken code; they are unpopulated or unfinished workflows. Fixing
them means implementing workflows and guiding users through them — not adding
more tabs or fake data.
