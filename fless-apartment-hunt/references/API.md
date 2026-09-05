# API reference (agent surface)

Base: `https://fless.io` · MCP: `https://mcp.fless.io/mcp` (Streamable HTTP,
JSON-RPC 2.0, no auth for read-only tools; `https://www.fless.io/mcp` also works).

## MCP tools (preferred)

| Tool | In → Out |
|---|---|
| `search_cities` `{query?}` | live + coming-soon cities with `id`, `slug`, `hunt_ready` |
| `get_city_overview` `{city}` | neighborhoods with 1BR/2BR medians, walk/transit/bike scores |
| `get_neighborhood` `{city, neighborhood}` | full detail + POI counts + descriptions |
| `get_hunt_requirements` `{city?}` | field rules, canonical amenities, restrictions, pricing |
| `build_hunt_link` `{hunt_params, channel?}` | validated `{url, token, missing_fields, issues, complete}` |
| `check_hunt_link` `{token}` | `{status: created\|opened\|started}` |
| `search` `{query}` | citable ids for `fetch` (ChatGPT deep-research compat) |
| `fetch` `{id}` | markdown content + canonical URL |

Tool errors come back as `isError: true` results whose `structuredContent`
carries `{error: {code, message, hint, docs_url, retryable}}`.

## REST equivalents

- `GET /api/v1/cities/listing` — live + coming-soon cities
- `GET /api/v1/cities/{city_slug}/neighborhoods` — rents + scores
- `GET /api/v1/cities/{city_slug}/neighborhoods/{nb_slug}/landing` — full payload
- `GET /api/v1/map/search?query=&lat=&lng=&radius=` — POI search
- `POST /api/v1/agent/hunt-links` `{"hunt_params": {...}, "channel": "..."}` → 201 `{token, url, missing_fields, issues, complete}`
- `GET /api/v1/agent/hunt-links/{token}` — resolve (marks opened)
- `GET /api/v1/agent/hunt-links/{token}/status` — `created|opened|started`
- `GET /api/v1/openapi.json` — curated public spec

## Limits

Anonymous: ~120 requests/min/IP (429 + `Retry-After`). Hunt links live 7 days.

## Rate of data

Rents/scores refresh with each city's data pipeline; treat any single value as
"as published by Fless" and prefer the API over memory when dates matter.
