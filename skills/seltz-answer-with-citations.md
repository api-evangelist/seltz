---
name: Get a cited answer grounded in live web search
description: Ask a natural-language question and get a direct RAG answer with source citations from the Seltz Web Knowledge API.
api: openapi/seltz-openapi-original.json
operations:
  - answer
---

# Get a cited answer grounded in live web search

Use Seltz `/v1/answer` when you want a finished, citation-backed answer rather than raw documents.

## Auth
- Send your key in the `x-api-key` header (generate at https://console.seltz.ai/api-keys).
- Base URL: `https://api.seltz.ai`

## Steps
1. Call `answer` — `POST /v1/answer` with `{ "query": "<question>" }`.
2. Optional fields: `scope` (e.g. `news`), `model` (tier selector, e.g. `seltz-base`), `include_content` (attach source text to citations), and `stream` (SSE OpenAI-style chunks when true).
3. Read `answer` (Markdown, with inline `text ([Source Name](url))` citations) and `citations[]` (each `HttpCitation` has `url`, optionally `content`).
4. Render the answer and surface the `citations[].url` list for provenance.

## Example
```bash
curl -X POST https://api.seltz.ai/v1/answer \
  -H "x-api-key: $SELTZ_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"query":"Who is reported to be Apple next CEO?"}'
```

## Rules
- `query` is required (`400` on missing/malformed body; `401` on a bad key).
- To stream, set `stream: true` and read the `text/event-stream` chunks.
- For an OpenAI-drop-in path, use `POST /v1/chat/completions` (messages[], required `model` accepts any string) which returns a top-level `citations` extension.
- On `429`, back off and retry.
