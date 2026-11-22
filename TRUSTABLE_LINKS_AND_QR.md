# Making a bank's own links and QR codes trustable

Last reviewed: 2026-11-22

## The premise

A bank cannot make a link trustable merely by publishing it. **Trust is decided
at the client.** The bank can publish verifiable evidence; a browser, mobile app
or partner wallet must verify that evidence before acting. Without a verifying
client, no system can guarantee trustworthiness.

This document describes the target architecture and states clearly what
VeriLinkOS provides today versus what remains to be built. It is distinct from
[LINK_SAFETY_EVIDENCE.md](LINK_SAFETY_EVIDENCE.md), which covers *inbound*
triage of suspicious links.

## Target architecture

### 1. Issue signed declarations, not bare URLs

For every customer-facing link or QR payload, the bank signs a canonical object:

```
{
  "schema_version": "bank-link-declaration.v1",
  "target_url": "https://bank.example/invoice/12345",
  "purpose": "invoice_notification",
  "expires_at": "2026-11-23T12:00:00Z",
  "nonce": "<unique>",
  "channel": "email",
  "bank_domain": "bank.example"
}
```

The signature is Ed25519 over the canonical bytes, with short expiry and a
single-use nonce where appropriate. Third-party shorteners are never used for
customer communication; the bank uses a controlled domain with no open
redirects.

### 2. Publish the verification key

The bank publishes its public key and a signed registry at a stable well-known
location, e.g. `/.well-known/bank-link-key.json`.

VeriLinkOS already ships real well-known primitives —
`/.well-known/verilink-trust-anchor.json`, `/.well-known/verilink-registry.json`
and `/.well-known/verilink-revocations.json` — so the discovery pattern exists.
Applying it to link declarations is future work.

### 3. Verify at the client

The verifying client (bank app, browser extension, partner wallet):

1. resolves the declaration from the bank domain,
2. verifies the signature against the published key,
3. checks expiry, nonce and revocation,
4. displays **"Verified: ACME Bank"** — or fails closed.

No signature, wrong domain, expired or revoked means the link is not presented
as verified. The badge must never be a decorative asset served from the page
itself.

### 4. QR codes carry the declaration

A QR code is a physical object that can be swapped. It should encode the signed
declaration (or a short code that resolves to it), and the client must display
the verified bank domain before opening anything. The dashboard's browser QR
decode is a step toward this, but today it only *reads* payloads for inbound
analysis; it does not issue or verify signed declarations.

### 5. Revoke and expire

A signed revocation list plus short lifetimes contain the damage from a leaked
key or link. Registry-level revocation exists today; link-level revocation does
not.

### 6. Monitor lookalikes — where the current tab helps

The Link Safety tab is currently an **inbound triage** surface:

- an analyst pastes a reported URL or decodes a QR image,
- the engine unfurls safely, applies heuristics and attributable intel,
- a signed `LNK-*` decision is produced with reasons and a hash/signature,
- confirmed lookalike domains are added through watchlist CRUD,
- append-only reviews and bundle exports preserve the evidence.

This protects the bank's defenders and incident workflow. It does **not** by
itself make the bank's outbound links trustable to customers.

## Shipped in VeriLinkOS (2026-11-22)

| Architecture step | Implementation |
|---|---|
| Issue signed declarations | `POST /v1/links/declarations` — canonical hash + Ed25519 signature, short expiry, nonce, domain binding |
| Publish the key | `/.well-known/bank-link-key.json` (public key) and `/.well-known/bank-link-revocations.json` (signed) |
| Verify | `POST /v1/links/verify` checks signature, expiry, revocation and domain; `GET /v1/links/d/{code}` resolves a short URL |
| QR | `GET /v1/links/d/{code}/qr` encodes the **signed declaration**, plus a generic `POST /v1/qr/encode` for any artifact |
| Revoke | `POST /v1/links/declarations/{id}/revoke`, published in the signed revocation list |
| Client verification | `static/js/link_declaration_verifier.js` verifies locally with WebCrypto against the bank's published key |
| Management UI | "Trustable Links & QR" tab: issue, verify, list, revoke, QR download |
| SDK | `issue_link_declaration()`, `verify_link_declaration()`, `qr_for()` |

A single base URL is configured through `VERILINK_BASE_URL` /
`VERILINK_PUBLIC_URL` / `VERILINK_ISSUER_URL` (see `DEPLOYMENT_CONFIG.md`).
Local, cloud and on-premise installs differ only in environment variables.

## Forward-looking only (not shipped or Roadmap)

- A browser extension or bank mobile app that calls the client verifier
  automatically before opening a link. The verifier module exists; the
  distributing client does not.
- A published cross-industry link-declaration specification.
- Integration with banks' existing PKI and certificate workflows.
- Brand monitoring and takedown beyond the inbound watchlist.

The honest current claim: a bank can now **issue signed link/QR declarations and
give clients a verifier that checks them against the bank's own published key**.
What remains is distribution of that verifier to customers, not the trust
mechanism itself.
