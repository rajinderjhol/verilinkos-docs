# VeriLinkOS API Reference

Comprehensive reference for the VeriLinkOS Enterprise API.

## 1. Guardian API (`/v1/guardian`)
- `POST /v1/guardian/enforce`: Evaluate AI action against policy.
- `GET /v1/verify/telemetry/stream`: Real-time telemetry stream (SSE).

## 2. Evidence API (`/v1/vap/receipt`)
- `POST /v1/vap/receipt/generate`: Generate a VAP receipt.
- `GET /v1/vap/receipt/{receipt_id}`: Retrieve receipt.
- `POST /v1/vap/receipt/verify`: Verify a VAP receipt.

## 3. Trust API (`/v1/trust`)
- `GET /v1/did/{did_uri}`: Resolve DID to agent identity.
- `POST /v1/trust-passport/verify`: Verify a trust passport.

## 4. Commerce API (`/v1/commerce`)
- `POST /v1/commerce/escrow/create`: Initialize autonomous escrow.

---
*For full specification, see [VAP v3.5 Specification](../protocols/vap/v3.5/specification.md).*
