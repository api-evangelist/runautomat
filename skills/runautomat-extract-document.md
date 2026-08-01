---
name: Extract structured data from a document
description: Use Automat's Document Extraction API to turn a PDF or image into structured JSON matching a preconfigured extractor's schema.
api: openapi/runautomat-extract-openapi.json
operations:
- extract
---

# Extract structured data from a document (Automat)

Automat's Extract API runs one configured **extractor** (`ext_` id) against a
document and returns structured JSON matching that extractor's schema.

## Prerequisites
- An organization API key (`ak_` prefix) from the Automat dashboard
  (Settings -> API Keys). Send it as `Authorization: Bearer ak_...`.
- An `extractorId` (create/configure the extractor in the dashboard first).

## Steps
1. **Authenticate.** Set header `Authorization: Bearer <your ak_ key>`. Keys are
   organization-scoped and grant access to all extractors in the org.
2. **Call `extract`** — `POST https://studio.runautomat.com/api/extract` with
   `multipart/form-data`:
   - `extractorId` (required) — the `ext_` extractor to run.
   - `file` (required) — binary upload, base64 string, data URL, or a public
     `http(s)` URL. When `file` is a string, also set `mimeType`
     (e.g. `application/pdf`, `image/png`).
   - Optional: `filename`, `extractorVersionId` (pin a version), `config`
     (model overrides), `timeout` (1-300 seconds).
   - Size limits: 4.5 MB for binary/base64/data-url; up to 35 MB via the URL
     option.
3. **Read the result.** On `200`, the response is `{ "data": { ... } }` where
   `data` matches your extractor's schema.

## Error handling
Errors use a flat `{ "error": "<code>", "message": "<text>" }` envelope (not
RFC 9457):
- `400` bad_request — missing/malformed `file` or `extractorId`.
- `401` unauthorized — missing/invalid/revoked API key.
- `422` — extractor not found/accessible, unsupported format, or unprocessable
  document.
- `500` — unexpected server error; retry or contact support.

## Notes
- The operation is synchronous; there are no list/pagination endpoints and no
  documented idempotency key.
- Reference: https://docs.runautomat.com/guides/getting-started/quickstart
