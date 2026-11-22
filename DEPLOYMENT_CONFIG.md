# Deployment URL configuration

Last reviewed: 2026-11-22

The application URL is defined **once**, in the environment. Code, templates and
SDKs derive everything else from it. No host should be hard-coded in a module.

## Variables

| Variable | Purpose | Local default |
|---|---|---|
| `VERILINK_BASE_URL` | Where this instance is reachable | `http://127.0.0.1:8001` |
| `VERILINK_PUBLIC_URL` | Externally advertised URL when behind a proxy/gateway | falls back to `VERILINK_BASE_URL` |
| `VERILINK_ISSUER_URL` | OAuth/JWT issuer identity when different from the public URL | falls back to `VERILINK_PUBLIC_URL` |
| `VERILINK_SITE_URL` | Corporate/marketing site — distinct from this deployment | `https://www.verilink.io` |
| `VERILINK_DOCS_URL` | Published documentation and specification links | `VERILINK_SITE_URL/docs` |

Set them in `.env` locally, and in your platform's secret/config store for cloud
and on-premise deployments. `.env.example` documents all three.

## What derives from them

- `/.well-known/verilink-registry.json`, `verilink-revocations.json`,
  `verilink-trust-anchor.json` and `jwks.json` URIs.
- MCP server card `issuer`, `oauthMetadata`, `provider.url` and trust anchor.
- DID document `serviceEndpoint`.
- SPDX `documentNamespace` in AIBOM/ML-BOM exports.
- Portal registration `base_url`.
- Corporate identity: `issued_by`, signer IDs, MCP `server_id`, contact domains,
  compliance contacts and HITL escalation addresses (`contact_domain()`).
- Documentation and specification links in well-known documents (`site_url()`).
- Signed link declarations' `verify_url` uses the **bank's** domain, not this
  deployment URL — that is intentional: the client must verify against the
  bank it is dealing with.

`VERILINK_SITE_URL` is deliberately separate: at full deployment the corporate
site may move to a different domain while the platform stays where it is.

## Single source of truth

All runtime code imports `verilink/config_urls.py`:

```python
from verilink.config_urls import base_url, public_url, issuer_url, well_known
```

Moving between local, cloud and customer premises requires changing only these
environment variables — no code or template edits.

## Multi-tenant SaaS and federation

The three deployment variables remain the single source of truth for the
**platform** URL. Tenant-specific identity is stored on the organisation row,
not in environment variables:

| Field | Purpose |
|---|---|
| `verified_domain` | Domain the tenant proved it controls; binds signed link declarations |
| `public_url` | Tenant's advertised URL (custom domain or SaaS subdomain) |
| `issuer_url` | Tenant-specific OAuth/JWT issuer |

### What is shipped

- **Per-tenant Ed25519 identities** derived from the master key via HKDF
  (`verilink/services/tenant_keys.py`). No private key is stored in the app DB.
- **Tenant-scoped discovery** at `/.well-known/t/{slug}/...` plus Host-based
  resolution for custom domains. Each tenant can publish its own trust anchor,
  link key and revocation list.
- **Domain ownership** — with `VERILINK_MULTI_TENANT=true`, a tenant can only
  sign link declarations for its `verified_domain`.
- **Federation peer metadata** — `FederationRegistry` stores
  `peer_wellknown_url` and `peer_public_key_fingerprint`; cross-org passport
  verification checks the issuer's anchor against its published key. Rows
  without a peer URL keep the legacy shared-key mode.
- **Data isolation** was already in place via org-scoped tables and tenant
  context derived from the API key.

### What remains

- Distributing a client verifier to customers (browser extension / bank app).
- External KMS backing for per-tenant keys in a regulated SaaS; HKDF derivation
  from one master key is appropriate for a single operator, not for mutually
  distrustful operators.
- Automated domain-verification challenges (DNS/HTTP) to populate
  `verified_domain` — today it is set by an administrator.

The earlier verdict — "sufficient only for one deployment of one organisation" —
no longer applies to the trust and discovery layer. It applies only to the
**platform URL variables themselves**, which were never meant to carry tenant
identity.
