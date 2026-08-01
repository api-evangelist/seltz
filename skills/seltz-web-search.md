---
name: Search the live web for agent context
description: Retrieve fresh, context-engineered web documents for an agent or RAG pipeline using the Seltz Web Knowledge API.
api: openapi/seltz-openapi-original.json
operations:
  - search
---

# Search the live web for agent context

Use Seltz `/v1/search` when an agent needs current, sourced web content to reason over.

## Auth
- Send your key in the `x-api-key` header. Generate keys at https://console.seltz.ai/api-keys.
- Base URL: `https://api.seltz.ai`

## Steps
1. Call `search` — `POST /v1/search` with a JSON body. Keep `query` short for best performance.
2. Optional narrowing fields: `max_results` (default 10), `include_domains`, `exclude_domains`, `from_date`, `to_date` (ISO 8601), and `scope` (e.g. `news`).
3. Read `documents[]` from the `SearchResponse`; each `Document` has `url`, `content`, and `published_date`.
4. Feed the document `content` into your model context; keep the `url` for attribution.

## Example
```bash
curl -X POST https://api.seltz.ai/v1/search \
  -H "x-api-key: $SELTZ_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"query":"latest EU AI Act enforcement","max_results":5,"scope":"news"}'
```

## Rules
- `query` is required; a missing/malformed body returns `400`, a bad key returns `401`.
- On `429`, back off and retry — the rate limit was exceeded.
- No idempotency key is needed; search is a read-only, non-mutating call.
