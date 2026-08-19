# VeriLinkOS API Reference

Comprehensive reference for the VeriLinkOS Enterprise API.

## 1. Guardian API (`/v1/guardian`)
- `POST /v1/guardian/enforce`: Evaluate AI action against policy.
- `GET /v1/guardian/telemetry`: Real-time telemetry stream (SSE).

## 2. Evidence API (`/v1/evidence`)
- `POST /v1/evidence/vap/generate`: Generate a VAP receipt.
- `GET /v1/evidence/vap/{receipt_id}`: Retrieve receipt.
- `POST /v1/evidence/vap/verify`: Independently verify a VAP receipt.

## 3. Trust API (`/v1/trust`)
- `GET /v1/trust/did/{did_uri}`: Resolve DID to agent identity.
- `GET /v1/trust/passport/{passport_id}`: Verify trust passport.

## 4. Commerce API (`/v1/commerce`)
- `POST /v1/commerce/escrow/create`: Initialize autonomous escrow.

---
*For full specification, see [VAP v3.5 Specification](../protocols/vap/v3.5/specification.md).*
