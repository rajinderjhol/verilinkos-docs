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

Set them in `.env` locally, and in your platform's secret/config store for cloud
and on-premise deployments. `.env.example` documents all three.

## What derives from them

- `/.well-known/verilink-registry.json`, `verilink-revocations.json`,
  `verilink-trust-anchor.json` and `jwks.json` URIs.
- MCP server card `issuer`, `oauthMetadata`, `provider.url` and trust anchor.
- DID document `serviceEndpoint`.
- SPDX `documentNamespace` in AIBOM/ML-BOM exports.
- Portal registration `base_url`.
- Signed link declarations' `verify_url` uses the **bank's** domain, not this
  deployment URL — that is intentional: the client must verify against the
  bank it is dealing with.

## Single source of truth

All runtime code imports `verilink/config_urls.py`:

```python
from verilink.config_urls import base_url, public_url, issuer_url, well_known
```

Moving between local, cloud and customer premises requires changing only these
environment variables — no code or template edits.

## Scope limit — read before a SaaS or federation claim

This single-URL model assumes **one deployment for one organisation**. It is not
yet sufficient for multi-tenant SaaS or for a federation of independent
operators:

- Signing keys (`AUTHORITY_PRIVATE_KEY`) and the trust anchor are process-wide,
  so every tenant would share one cryptographic identity.
- Well-known paths are global and unauthenticated; they cannot distinguish
  tenants.
- `FederationRegistry` records org pairs and trust levels but no peer endpoint
  or peer public key, so cross-organisation verification relies on the shared
  key rather than on each issuer's own published anchor.
- Link declarations accept any `bank_domain` string; a tenant is not yet proved
  to control the domain it signs for.

Data isolation is in place (org-scoped tables with tenant context from the API
key). The trust and discovery layer is not. SaaS/federation work requires
per-tenant keys, tenant-scoped discovery paths, domain-ownership proof and peer
trust metadata — not additional environment variables.
