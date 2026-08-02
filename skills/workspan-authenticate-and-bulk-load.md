---
name: Authenticate and bulk load objects into WorkSpan
description: Get an OAuth 2.0 access token and bulk load opportunities or other objects (JSON/CSV/XLSX) through
  the WorkSpan Bulk API, then poll load status.
api: openapi/workspan-bulk-openapi.yml
operations:
- post-token
- upload_url
- bulkload
- bulkload_opportunities
- status
---

## When to use
You need to push CRM opportunities or other object data into WorkSpan in bulk.

## Steps
1. **Authenticate** — `post-token` (Authentication API, `POST /oauth/token`): OAuth 2.0 client-credentials
   grant. The response carries `access_token` (Bearer, `expires_in: 3600`) and a `refresh_token`. Add the
   `x-ws-env` header to target the WorkSpan sandbox environment. Every subsequent call needs the Bearer
   token **and** the `Ocp-Apim-Subscription-Key` header.
2. **(File uploads)** — `upload_url` to obtain an upload URL when sending CSV/XLSX files.
3. **Load** — `bulkload` (`POST /bulkload?integration_id={integration_id}`) with JSON/CSV inline or an
   attached file; use `bulkload_opportunities` for the opportunity-specific path. `integration_id`
   identifies the configured integration.
4. **Poll** — `status` to check load progress and retrieve row-level errors (`include_errors`,
   `max_errors` query parameters).

## Rules
- No idempotency contract is documented: do not blind-retry a submitted load; check `status` first
  (see `conventions/workspan-conventions.yml`).
- Errors are plain JSON, not RFC 9457 (`errors/workspan-problem-types.yml`).
